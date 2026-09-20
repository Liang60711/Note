# Service

## 概念

Pod 的 IP 通常是暫時性的：Pod 被刪除、重建或重新排程後，IP 可能改變。Service 提供一個穩定的存取入口，將流量轉送到符合條件的 Pod。

Service 主要解決以下問題：

- 提供穩定的虛擬 IP 和 DNS 名稱。
- 透過 Label Selector 找到後端 Pod。
- 將流量分散到多個 Pod。
- 隔離使用者不需要知道 Pod 的實際 IP。

```text
Client -> Service（穩定入口） -> Pod（可能持續變動）
```

Service 本身通常不直接建立 Pod。它透過 Selector 找到 Pod，並由 Kubernetes 建立 EndpointSlice，記錄目前可用的後端端點。

## Service 與 Pod 的關係
Service 使用 `spec.selector` 選擇具有指定 Label 的 Pod：

```yaml
selector:
	app: nginx
```

上面的設定會選取所有帶有 `app: nginx` Label 的 Pod。Service 的 Selector 必須與 Pod 的 Label 相符，否則 Service 不會有後端端點。

```yaml
Service selector: app=nginx
            |
            v
Pod labels: app=nginx, version=v1
```

比較: 
- Worked Controller 的 Selector 是用來管理 Pod。
- Service 的 Selector 是用來轉送流量。

兩者可以使用相同的 Label，但責任不同。

## Service 底層實作：iptables 與 IPVS


### kube-proxy 的工作

`kube-proxy` 會監聽 API Server 中的 Service 和 EndpointSlice。當 Service、Pod 或端點發生變更時，kube-proxy 會更新所在 Node 的流量轉送規則。

在 iptables 模式下，kube-proxy 不會替每個連線啟動一個代理程序，而是讓 Linux kernel 直接依照 iptables 規則處理封包。因此 Service 的轉送主要發生在 kernel network stack 中。



### iptables 轉送流程

假設：

```text
Service ClusterIP: 10.96.0.10:80
Pod endpoint:      10.244.1.5:8080
```

Client 連線到 `10.96.0.10:80` 時，大致會經過以下流程：

1. 封包抵達 Node，目的地是 Service 的 ClusterIP 和 `port`。
2. iptables 的 Service 規則比對目的 IP 和 port。
3. 規則將封包導向對應的 Kubernetes chain，例如 `KUBE-SERVICES`、`KUBE-SVC-*`。
4. Service chain 從 EndpointSlice 的後端端點中選擇一個 Pod。
5. 封包套用 DNAT，將目的地從 `10.96.0.10:80` 改成 `10.244.1.5:8080`。
6. 封包透過 CNI 網路送到 Pod。
7. 回應封包透過 conntrack 還原連線資訊，讓 Client 看到的仍是 Service 的 IP 和 port。

```text
Client
	|
	|  destination: 10.96.0.10:80
	v
iptables KUBE-SERVICES
	|
	v
KUBE-SVC-*（選擇後端）
	|
	v
KUBE-SEP-*（指定 Pod endpoint）
	|
	|  DNAT: 10.96.0.10:80 -> 10.244.1.5:8080
	v
Pod
```

`KUBE-SEP-*` 可理解為指向特定 endpoint 的規則。實際 chain 名稱與規則內容會依 Kubernetes 版本和 kube-proxy 設定而變化，不應將 chain 名稱當作固定 API。

### iptables 常見規則概念

- `KUBE-SERVICES`：根據 Service 的 ClusterIP 和 port 找到對應的 Service chain。
- `KUBE-SVC-*`：代表某個 Service 的處理流程，通常負責選擇後端 endpoint。
- `KUBE-SEP-*`：代表某個 Service endpoint，通常包含將封包 DNAT 到 Pod IP 的規則。
- `KUBE-NODEPORTS`：處理 NodePort 進入的流量。
- `KUBE-POSTROUTING`：處理部分需要 SNAT 的回程或跨網路流量。

iptables 模式通常使用機率分配的規則選擇後端，而不是在 Service 上建立一個真正的程序。因此後端 Pod 數量或 IP 改變時，kube-proxy 必須重新同步規則。

### DNAT 與 SNAT

- **DNAT（Destination NAT）**：把目的地從 Service ClusterIP 和 `port` 改成 Pod IP 和 `targetPort`。
- **SNAT（Source NAT）**：把來源 IP 改成 Node IP，使回程封包能正確返回；是否使用 SNAT 會受到流量路徑與 `externalTrafficPolicy` 等設定影響。
- **conntrack**：Linux 追蹤連線的 NAT 狀態，確保同一條連線的回應可以套用相反方向的 NAT。

### 查看 iptables 規則

以下指令需要在對應的 Node 上執行，且通常需要 root 權限：

```sh
# 查看 Service 相關的 nat table 規則
sudo iptables -t nat -L KUBE-SERVICES -n -v

# 查看 kube-proxy 建立的自訂 chain
sudo iptables -t nat -L KUBE-SVC-XXXX -n -v
sudo iptables -t nat -L KUBE-SEP-XXXX -n -v

# 搜尋某個 ClusterIP
sudo iptables-save -t nat | grep '10.96.0.10'
```

有些系統使用 `iptables-nft` 作為 iptables 指令的相容層，因此看到的底層規則可能實際由 nftables 實作。

### IPVS 實作

IPVS（IP Virtual Server）是 Linux kernel 提供的 Layer 4 負載平衡功能。kube-proxy 使用 IPVS 模式時，會將 Service 建立為 virtual server，並將 Pod endpoint 加入 real server 清單。封包命中 Service 的 ClusterIP 和 port 後，由 IPVS 選擇一個後端 Pod。

```text
Service ClusterIP:port
		  |
		  v
IPVS virtual server
		  |
		  v
IPVS real servers（Pod IP:targetPort）
		  |
		  v
Pod
```

IPVS 支援多種負載平衡演算法，例如：

- `rr`（round-robin）：依序選擇後端。
- `lc`（least connections）：選擇目前連線數較少的後端。
- `dh`（destination hashing）：依目的地進行雜湊選擇。
- `sh`（source hashing）：依來源進行雜湊選擇。

與 iptables 相比，IPVS 將 Service 和 endpoint 儲存在 kernel 的 IPVS table 中，不需要為大量 endpoint 建立很長的 iptables 規則鏈，因此在 Service 或 Pod 數量很多時，規則同步與查找通常更有效率。不過實際效能仍取決於 Kubernetes 版本、kernel、網路插件與叢集規模。

IPVS 模式通常仍會使用部分 iptables 規則處理 NodePort、SNAT、封包標記或其他輔助流程。因此使用 IPVS 不代表 Node 上完全沒有 iptables 規則。

### 查看 IPVS 規則

以下指令需要在對應的 Node 上執行，且通常需要 root 權限：

```sh
# 查看所有 IPVS virtual server 與 real server
sudo ipvsadm -Ln

# 顯示連線統計資料
sudo ipvsadm -Ln --stats

# 顯示連線與 timeout 資訊
sudo ipvsadm -Ln --timeout
```

也可以查看 kube-proxy 使用的模式：

```sh
kubectl -n kube-system get configmap kube-proxy -o yaml
```

搜尋 `mode` 設定；常見值包括 `iptables`、`ipvs` 和 `nftables`，實際可用模式依 Kubernetes 版本與環境而定。若叢集使用 IPVS 或 eBPF，則不一定能在 iptables 中看到完整的 Service 轉送規則。

## Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-service
	labels:
		app: nginx
spec:
	type: ClusterIP # Service 類型；未指定時預設為 ClusterIP
	selector:
		app: nginx # 將流量轉送到帶有此 Label 的 Pod
	ports:
		- name: http
			protocol: TCP
			port: 80 # Service 對外提供的連接埠
			targetPort: 80 # Pod container 實際監聽的連接埠
```

`port` 和 `targetPort` 不一定要相同。例如 Service 可以使用 `port: 80` 接收請求，再轉送到 Pod 的 `targetPort: 8080`。

```yaml
ports:
	- name: http
		port: 80
		targetPort: 8080
```

`targetPort` 也可以使用容器連接埠名稱，但 Pod 必須先定義同名的 port：

```yaml
# Pod 的 container port
ports:
	- name: http
		containerPort: 8080

# Service 的 targetPort
ports:
	- name: http
		port: 80
		targetPort: http
```

## Service 類型

| Type             | 主要用途                 | IP                    | kube-proxy | 對外暴露 |
| ---------------- | -------------------- | --------------------- | ---------- | ---- |
| **ClusterIP**    | Cluster 內部服務         | ClusterIP             | ✅          | ❌    |
| **NodePort**     | 從 Node IP 進入 Service | ClusterIP + NodePort  | ✅          | ✅    |
| **LoadBalancer** | 透過外部 LB 進入 Service   | ClusterIP + NodePort* | ✅          | ✅    |
| **ExternalName** | 連到 Cluster 外部服務      | ❌                     | ❌          | ❌    |

<br/>

<br/>


### ClusterIP

`ClusterIP` 是預設類型，只能從 Kubernetes Cluster 內部存取。適合內部微服務，例如 API、資料庫或後端服務。

```yaml
apiVersion: v1
kind: Service
metadata:
	name: backend
spec:
	type: ClusterIP
	selector:
		app: backend
	ports:
		- port: 8080
			targetPort: 8080
```

### NodePort

`NodePort` 會在每個 Node 開放一個連接埠，外部流量可以透過任一 Node 的 IP 和 NodePort 進入 Service。

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-nodeport
spec:
	type: NodePort
	selector:
		app: nginx
	ports:
		- port: 80
			targetPort: 80
			nodePort: 30080 # 通常範圍為 30000-32767
```

存取方式：

```text
<Node IP>:30080
```

NodePort 通常適合測試或沒有 Cloud Load Balancer 的環境。正式環境常搭配 LoadBalancer 或 Ingress 使用。

### LoadBalancer

`LoadBalancer` 會請求底層雲端平台建立外部 Load Balancer，並將流量導向 Service。實際行為取決於使用的雲端平台或 LoadBalancer Controller。

雖然 LoadBalacer 和 NodePort 兩個都屬於 Service 資源，但兩者解決的問題不同。

- NodePort: 讓 Kubernetes Service 能從叢集外部被存取。

- LoadBalacer: 讓外部透過雲端 Load Balancer 進 Kubernetes，並處理對外流量分配，通常不能自行建置，需要雲開發商提供。


--- 

第一種，預設情況下，建立 LoadBalancer 時，會再產生一層 NodePort 轉發。

```
Client
 ↓
Load Balancer
 ↓
NodePort
 ↓
kube-proxy
 ↓
Pod
```

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-loadbalancer
spec:
	type: LoadBalancer
	selector:
		app: nginx
	ports:
		- port: 80
			targetPort: 80
```

---

第二種，當明確配置不建立 NodePort 時，轉發路徑直接轉發給 Pod。
```
Client
 ↓
Load Balancer
 ↓
Pod
```
```yaml
apiVersion: v1
kind: Service
metadata:
  	name: nginx-loadbalancer
spec:
	type: LoadBalancer
	allocateLoadBalancerNodePorts: false # 不要使用 NodePort
	selector:
		app: nginx
	ports:
		- port: 80
			targetPort: 80
			nodePort: 30080
```



在本機叢集或沒有雲端整合的環境中，`EXTERNAL-IP` 可能長時間顯示 `<pending>`。

### ExternalName

將集群外部的服務引入到集群內部來。

`ExternalName` 是在 Kubernetes 內建立一個 Service DNS 名稱，讓 Pod 可以用這個內部名稱，透過 DNS CNAME 指向外部的 domain，最後再由 DNS 解析出外部 IP。

```
Pod
 ↓
my-db.default.svc.cluster.local
 ↓
CoreDNS
 ↓
CNAME database.example.com
 ↓
DNS 再解析出真正的 IP
 ↓
外部 Database
```

```yaml
apiVersion: v1
kind: Service
metadata:
	name: external-database
spec:
	type: ExternalName
	externalName: database.example.com
```

它適合將叢集外部服務以 Kubernetes Service 名稱提供給叢集內的應用程式使用，但不會替外部服務提供健康檢查或負載平衡。


以下是分工: 

| 元件                         | 負責                                    |
| -------------------------- | ------------------------------------- |
| **Service / ExternalName** | 定義 `mydb → db.example.com` 這個 mapping |
| **API Server / etcd**      | 保存這個 Service 設定                       |
| **CoreDNS**                | 讀取 Kubernetes 的 Service 資訊，並回答 DNS 查詢 |
| **外部 DNS**                 | 負責 `db.example.com → IP`              |



<br/>

<br/>



### Headless Service

將 `clusterIP` 設定為 `None`，就會建立 Headless Service。它不提供單一虛擬 IP，而是讓 DNS 回傳後端 Pod 的 IP。

```yaml
apiVersion: v1
kind: Service
metadata:
	name: mysql
spec:
	clusterIP: None
	selector:
		app: mysql
	ports:
		- port: 3306
			targetPort: 3306
```

Headless Service 常搭配 StatefulSet 使用，讓應用程式可以找到每一個具有穩定名稱的 Pod。

```text
mysql-0.mysql.default.svc.cluster.local
mysql-1.mysql.default.svc.cluster.local
```

Headless Service 不代表一定會提供資料庫叢集功能。選主、複寫和資料一致性仍需由資料庫或 Operator 負責。

## Service DNS

叢集內的 Pod 通常可以透過 Service 名稱存取服務：

```text
<service-name>.<namespace>.svc.cluster.local
```

例如：

```text
backend.default.svc.cluster.local
```

同一個 Namespace 內通常可以直接使用短名稱：

```text
http://backend:8080
```

跨 Namespace 存取時，至少需要使用：

```text
http://backend.backend-ns:8080
```

## Service 與 Ingress 的差異

| 資源 | 主要用途 | 常見流量範圍 |
|---|---|---|
| Service | 提供穩定入口並將流量轉送到 Pod | 叢集內部或單一服務 |
| Ingress | 依網域名稱或 URL 路徑分流 | HTTP/HTTPS 外部流量 |

Ingress 通常會將流量轉送到 Service，而不是直接轉送到 Pod：

```text
Internet -> Ingress -> Service -> Pod
```

Service 解決「如何找到一組 Pod」；Ingress 解決「外部 HTTP/HTTPS 請求要導向哪個 Service」。

## 沒有 Selector 的 Service

Service 可以不設定 Selector，改由管理者手動建立 EndpointSlice，適合：

- 將叢集內的名稱對應到叢集外服務。
- 暫時切換後端服務。
- 後端端點不由 Pod Label 管理。

```yaml
apiVersion: v1
kind: Service
metadata:
	name: legacy-service
spec:
	ports:
		- port: 80
			targetPort: 8080
```

沒有 Selector 的 Service 不會自動產生後端端點，必須另外建立 EndpointSlice 或其他對應設定。

## 常用操作

```sh
# 建立或更新 Service
kubectl apply -f service.yaml

# 查看 Service
kubectl get service
kubectl get svc nginx-service

# 查看 Service 詳細資訊與事件
kubectl describe service nginx-service

# 查看 Service 對應的 EndpointSlice
kubectl get endpointslice
kubectl get endpointslice -l kubernetes.io/service-name=nginx-service

# 查看 Service 的 YAML
kubectl get service nginx-service -o yaml

# 查看 Pod Label，確認是否符合 Service Selector
kubectl get pod --show-labels

# 查看 Pod IP 和所在 Node
kubectl get pod -o wide

# 刪除 Service
kubectl delete service nginx-service
```

## 連線測試

可以在叢集內建立臨時 Pod，測試 DNS 與 Service 連線：

```sh
kubectl run curl --rm -it --image=curlimages/curl -- sh
```

進入臨時 Pod 後：

```sh
curl http://nginx-service
curl http://nginx-service.default.svc.cluster.local
```

若 Service 使用非 HTTP 協定，可以使用對應的網路工具，例如 `nc` 或資料庫 Client 測試連線。

## 常見問題與除錯流程

### Service 沒有後端端點

```sh
kubectl get service nginx-service
kubectl get endpointslice -l kubernetes.io/service-name=nginx-service
kubectl get pod --show-labels
```

優先檢查：

1. Service 的 `selector` 是否拼寫正確。
2. Pod 是否存在對應的 Label。
3. Pod 是否處於 `Running` 且已通過 Readiness Probe。
4. Service 和 Pod 是否位於相同 Namespace。

### Service 有端點但連線失敗

優先檢查：

1. `port` 和 `targetPort` 是否正確對應。
2. Container 是否真的監聽該連接埠。
3. NetworkPolicy 是否阻擋流量。
4. 應用程式是否只監聽 `127.0.0.1`，而不是 `0.0.0.0`。
5. Pod 的 Readiness Probe 是否正確反映服務狀態。

### LoadBalancer 的外部 IP 是 pending

這通常表示叢集沒有可用的雲端 Load Balancer 整合。可以：

- 在雲端環境確認 Cloud Controller Manager 和權限設定。
- 在本機環境使用 NodePort、MetalLB 或 Ingress Controller。

## 重點整理

- Service 提供穩定入口，Pod IP 則可能隨時改變。
- Service 通常透過 Selector 選擇 Pod，並由 EndpointSlice 保存端點。
- `ClusterIP` 用於叢集內部；`NodePort` 開放 Node 連接埠；`LoadBalancer` 通常建立雲端外部負載平衡器。
- `clusterIP: None` 是 Headless Service，常搭配 StatefulSet。
- `port` 是 Service 的連接埠，`targetPort` 是 Pod 的目標連接埠。
- Service 沒有端點時，先檢查 Selector、Pod Label、Namespace 和 Readiness 狀態。
