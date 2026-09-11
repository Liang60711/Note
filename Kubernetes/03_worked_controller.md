# 工作負載控制器 Worked Controller

## 概念

Kubernetes Controller 會持續比較：

`Desired State（期望狀態）` 和 `Actual State（實際狀態）`

如果兩者不同，Controller 就會採取動作，直到實際狀態符合期望狀態。這個持續調整的過程稱為 `reconciliation` (調和)。


## 工作負載控制器總覽

| 資源 | 是否控制 Pod | 說明 |
|---|---|---|
| Deployment | 是，間接控制 | 管理 ReplicaSet，再由 ReplicaSet 管理 Pod |
| ReplicaSet | 是，直接控制 | 維持指定數量的 Pod |
| StatefulSet | 是 | 管理具有穩定名稱、網路與儲存的 Pod |
| DaemonSet | 是 | 確保每個符合條件的 Node 執行一個 Pod |
| Job | 是 | 確保一次性工作成功完成 |
| CronJob | 間接控制 | 依排程建立 Job，再由 Job 建立 Pod |

這些資源都屬於 Kubernetes 的工作負載控制器（workload controllers）：



## 為什麼不直接建立 Pod

直接建立 Pod 時，Pod 被刪除或所在 Node 故障後，通常不會自動補回。實務上應該交給 Controller 管理 Pod，例如：

- Deployment：管理無狀態服務，支援滾動更新與回滾。
- StatefulSet：管理需要穩定名稱、穩定網路或持久化儲存的服務。
- DaemonSet：確保每個符合條件的 Node 都執行一個 Pod。
- Job：執行一次性工作，直到成功完成。
- CronJob：依排程週期建立 Job。


## ReplicationController 和 ReplicaSet

### ReplicationController (舊版本)

早期的 Pod 副本控制器，透過 `replicas` 維持指定數量的 Pod。新專案通常使用 ReplicaSet 或 Deployment，不再直接使用 ReplicationController。

### ReplicaSet

幾個重點: 
* 通常不會直接使用 RS 建立 Pod，會使用 Deployment 資源，因為是封裝過的資源，功能多了 Rollback, 版本更新策略。

* 若強制將某 RS 下的 Pod 修改 label 成非符合 selector。

    1. RS 會檢測到少了一個符合 selector 的 Pod，會再創建一個 Pod。

    2. 被改 label 的 Pod 不會被殺死，但會變成孤兒 Pod。

ReplicaSet 的責任是維持符合 selector 的 Pod 數量：

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: nginx-rs
spec:
	replicas: 3     # 副本數
	selector:       # 需要管理的 Pod (使用標籤指定)
		matchLabels:
			app: nginx
	template:       # Pod 的定義模板
		metadata:
			labels:
				app: nginx
		spec:
			containers:
				- name: nginx
					image: nginx:1.27
					ports:
						- containerPort: 80
```

`spec.selector.matchLabels` 必須能匹配 `spec.template.metadata.labels`，否則資源會被拒絕或無法正確管理。

通常不直接建立 ReplicaSet，而是使用 Deployment，讓 Deployment 負責管理 ReplicaSet 的版本與更新。

## Deployment

Deployment 適合無狀態應用程式，例如 Web API、前端或 nginx。它會建立並管理 ReplicaSet，ReplicaSet 再管理實際的 Pod。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
	name: nginx-deployment
	labels:
		app: nginx
spec:
	replicas: 3
	revisionHistoryLimit: 3		# 保留的舊 ReplicaSet revision 數量
	strategy:
		type: RollingUpdate
		rollingUpdate:
			maxUnavailable: 1	# 更新期間，允許不可用的 Pod 數量 (可用百分比表示)
			maxSurge: 1			# 更新期間，允許超過 replicas 的 Pod 數量 (可用百分比表示)
	selector:
		matchLabels:
			app: nginx
	template:
		metadata:
			labels:
				app: nginx
		spec:
			containers:
				- name: nginx
					image: nginx:1.27
					ports:
						- containerPort: 80
					readinessProbe:
						httpGet:
							path: /
							port: 80
						initialDelaySeconds: 5
						periodSeconds: 10
```

### Deployment 與 ReplicaSet 的關係

Deployment 本身不直接建立 Pod，而是透過 ReplicaSet 管理 Pod：

1. Deployment 根據 `spec.template` 建立 ReplicaSet。
2. ReplicaSet 依照 `spec.replicas` 維持指定數量的 Pod。
3. ReplicaSet 建立的 Pod 會帶有 `pod-template-hash` Label，Deployment 透過這個值區分不同版本。
4. Deployment 會保留舊 ReplicaSet，讓更新歷史與回滾可以運作；保留數量(幾個舊RS版本)由 `revisionHistoryLimit` 控制。

因此，Deployment、ReplicaSet 和 Pod 的關係可以簡化為：

```text
Deployment
├── ReplicaSet（目前版本）
│   └── Pod x 3
└── ReplicaSet（舊版本，可能保留供回滾）
    └── Pod x 0
```

只有 Pod template 的內容變更，例如映像檔、環境變數或 Probe，才會建立新的 ReplicaSet；單純修改 Deployment 的副本數不會建立新版本。

### RollingUpdate 更新機制

範例中的 `strategy.type: RollingUpdate` 會依序執行以下步驟：

1. 修改 Deployment 的 Pod template，例如將 `nginx:1.27` 更新為 `nginx:1.28`。
2. Deployment 建立新的 ReplicaSet，並使用新的 `pod-template-hash`。
3. 新 ReplicaSet 逐步建立新 Pod；舊 ReplicaSet 暫時保留，讓服務不中斷。
4. 新 Pod 通過 `readinessProbe` 後，才會被 Service 視為可用端點。
5. 當新 Pod 可用數量增加，舊 ReplicaSet 逐步縮減，直到所有 Pod 都切換到新版本。
6. Deployment 完成更新後，舊 ReplicaSet 通常縮減為 0 個 Pod，但仍保留其版本資訊供回滾。
	- 舊的 ReplicaSet 會保留，用來做回滾或版本紀錄，但 ReplicaSet 底下的 Pod 會刪除。

更新過程會受到以下設定限制：

- `maxSurge: 1`：更新期間最多比 `replicas` 多建立 1 個 Pod。
- `maxUnavailable: 1`：更新期間最多允許 1 個 Pod 不可用。
- `readinessProbe`：新 Pod 尚未就緒時，不會接收 Service 流量。

若新版本的 Pod 無法啟動或無法通過 readiness probe，RollingUpdate 可能會停在更新中，此時可使用 `kubectl rollout status`、`kubectl describe` 和事件記錄進行除錯。

### 回滾機制

執行 `kubectl rollout undo` 時，Deployment 會將 Pod template 還原到舊版本，並重新調整對應 ReplicaSet 的副本數。舊 ReplicaSet 因此不只是歷史紀錄，也保存了可重新啟動的版本設定。

### Deployment 常用操作

```sh
# 比較本地 YAML 與叢集中目前資源的差異，不修改資源
kubectl diff -f deployment.yaml

# 建立或套用 Deployment
# 只更新 YAML 中有指定且變更的欄位
kubectl apply -f deployment.yaml

# 完全覆蓋套用中的 Deployment
kubectl replace -f deployment.yaml

# 查看 Deployment、ReplicaSet、Pod
kubectl get deployment,rs,pod -l app=nginx

# 手動調整 Deployment 的副本數
kubectl scale deployment/nginx-deployment --replicas=5

# 建立 HPA，依 CPU 使用率自動調整副本數（需要 Metrics Server）
kubectl autoscale deployment/nginx-deployment --min=2 --max=10 --cpu-percent=70

# 更新映像檔
kubectl set image deployment/nginx-deployment nginx=nginx:1.28

# 查看更新進度
kubectl rollout status deployment/nginx-deployment

# 查看歷史版本
kubectl rollout history deployment/nginx-deployment

# 回滾到上一個版本
kubectl rollout undo deployment/nginx-deployment

# 回滾到指定的版本，例如 revision 1
kubectl rollout undo deployment/nginx-deployment --to-revision=1

# 暫停與恢復更新
kubectl rollout pause deployment/nginx-deployment
kubectl rollout resume deployment/nginx-deployment
```

補充回滾: 
- 不指定 `--to-revision`：回滾到上一個 revision，適合快速撤回最近一次更新。回滾會建立新的 revision，不會直接改寫原本的版本編號；例如目前為 3，回滾到 2 的內容後會建立 revision 4，再次回滾則會建立 revision 5，內容回到 revision 3；通常這樣很亂，會直接指定版本號。

- 指定 `--to-revision=1`：回滾到指定的 revision，不一定是上一個版本；可先使用 `kubectl rollout history deployment/nginx-deployment` 查看版本編號。



## StatefulSet

StatefulSet 適合資料庫、訊息佇列等需要身分的應用程式。和 Deployment 的 Pod 名稱可能變動不同，StatefulSet 會提供：

- 穩定且有序的 Pod 名稱，例如 `mysql-0`、`mysql-1`。
- 穩定的網路身分，通常搭配 Headless Service。
- 每個 Pod 可綁定自己的 PersistentVolumeClaim。
- 建立、刪除與更新通常具有順序性。

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
	name: web
spec:
	serviceName: web
	replicas: 2
	selector:
		matchLabels:
			app: web
	template:
		metadata:
			labels:
				app: web
		spec:
			containers:
				- name: nginx
					image: nginx:1.27
					volumeMounts:
						- name: data
							mountPath: /usr/share/nginx/html
	volumeClaimTemplates:
		- metadata:
				name: data
			spec:
				accessModes: ["ReadWriteOnce"]
				resources:
					requests:
						storage: 1Gi
```

StatefulSet 不會自動讓應用程式具備資料一致性。資料庫叢集的選主、複寫與故障處理，仍需由資料庫本身或 Operator 負責。

## DaemonSet

DaemonSet 的用途：   
確保 Kubernetes 集群中「每個符合條件的 Node」上，都只能跑一個 Pod。


DaemonSet 很適合「每台 Node 都需要一份」的服務，例如：  
- Log agent，例如 Filebeat。
- Monitoring agent，例如 Prometheus。
- 網路相關 agent，例如 CNI plugin。


DaemonSet 與 Deployment 比較: 
- Deployment：我要「總共 N 個 Pod」
- DaemonSet：我要「每個 Node 一個 Pod」

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
	name: node-agent
spec:
	selector:   
		matchLabels:
			app: node-agent
	template:
		metadata:
			labels:
				app: node-agent
		spec:
			tolerations:
				- key: dedicated 		# 對應 Node Taint 的 key
					operator: Equal 	# 要求 key 的 value 必須相等
					value: agent 		# 對應 Node Taint 的 value
					effect: NoSchedule 	# 容忍此效果，允許 Pod 排程到該 Node
			containers:
				- name: agent
					image: busybox:1.36
					command: ["sh", "-c", "while true; do sleep 3600; done"]
```

若只想部署到特定 Node，可以使用 `nodeSelector`、Node affinity 或搭配 `tolerations` 讓 Pod 能執行在 Taint Node 上。

## Job

Job 代表一次性工作，會建立 Pod 並確保指定數量的工作`成功完成`(容器的 exit code 返回碼為0) 。Pod 成功結束後，Job 不會再建立新的 Pod。

否則使用一般 Deployment 會有一種情況，當 MySQL 備份後，程序結束，容器銷毀，Deployment 為了確保有 1 個 Pod 運行，又再重新建立一個 Pod，又再備份一次，無限循環。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
	name: data-job
spec:
	completions: 3
	parallelism: 2
	backoffLimit: 4
	template:
		spec:
			restartPolicy: Never		# 僅 Never 或 OnFailure 選項
			containers:
				- name: worker
					image: busybox:1.36
					command: ["sh", "-c", "echo processing; sleep 5"]
```

- `completions`：需要成功完成的總數。
- `parallelism`：同時執行的 Pod 數量上限。
- `backoffLimit`：失敗後最多重試次數。
- Job 的 Pod template 必須設定 `restartPolicy: Never` 或 `OnFailure`。

## CronJob

CronJob 依照 Cron 表達式週期性建立 Job。

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
	name: cleanup
spec:
	schedule: "0 2 * * *"
	concurrencyPolicy: Forbid 		# 控制同一個 CronJob 的 Job 是否可同時執行；Forbid 表示前一個仍在執行時，不建立下一個
	successfulJobsHistoryLimit: 3	# 最多保留 3 個已成功完成的 Job
	failedJobsHistoryLimit: 1		# 最多保留 1 個失敗的 Job
	jobTemplate:
		spec:
			backoffLimit: 2
			template:
				spec:
					restartPolicy: OnFailure
					containers:
						- name: cleanup
							image: busybox:1.36
							command: ["sh", "-c", "echo cleanup"]
```

`concurrencyPolicy` (併行執行策略) 常見值：

- `Allow`：允許多個排程工作同時執行，預設值。 
- `Forbid`：上一個仍在執行時，不建立下一個。
- `Replace`：停止上一個，再建立新的 Job。

## 控制器與 Service 的關係

Controller 負責維持 Pod 的數量與生命週期；Service 負責提供穩定的存取入口與流量轉送。兩者通常透過 Label/Selector 配合：

```yaml
selector:
	app: nginx
```

Controller 的 selector 用來找它要管理的 Pod，Service 的 selector 用來找它要轉送流量的 Pod。兩者可以相同，但責任不同。

## 常用除錯流程

```sh
# 查看控制器與 Pod
kubectl get deployment,rs,pod -o wide

# 查看控制器事件與條件
kubectl describe deployment <deployment-name>

# 查看 Pod 是否被正確納入控制器
kubectl get pod --show-labels

# 查看最近事件
kubectl get events --sort-by=.lastTimestamp

# 檢查 Deployment 是否卡在更新
kubectl rollout status deployment/<deployment-name>
```

如果 Pod 數量不符合預期，優先檢查 `selector` 是否匹配、Pod template 的 Label 是否正確、Node 是否有足夠資源，以及 Pod 是否因 ImagePull、Probe 或排程問題而失敗。

