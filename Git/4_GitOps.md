## GitOps 概念

前提須建立幾個服務:
1. Gitlab Server
    - 建議使用 Docker Compose 或是使用 Omnibus 直接安裝在虛擬機上；與 K8S 的容器作區隔。
    - 包含K8S配置Repo、其他服務的原始碼Repo。

2. Gitlab CI Runner
    - 使用 K8S 啟動此服務。
    - 用來跑 CI 腳本和推送新版本到 Registry。

3. Registry Server
    - 建議使用 Docker Compose
    - 使用 Harbor，比官方的 Registry 好用。

4. ArgoCD Server
    - 使用 K8S 啟動此服務。

5. K8S Cluster
    - 啟動各種應用程式。

<br/>

<br/>

---

呈上，可以使用3台獨立的主機來建置，以免耦合。

|主機名稱|執行服務|主要負擔|
|:--|:--|:--|
|Server 1|	GitLab Server|	處理 Git Commit、CI/CD 邏輯、存放程式碼。|
|Server 2|	Registry (Harbor)|	存放與分發 Image。硬碟要大，網路要快。|
|Server 3...N|	K8s Nodes|	執行各個專案程式、執行 GitLab Runner、執行 ArgoCD。|


<br/>

<br/>


## Gitlab CI Runner

執行的事情如下步驟: 

1. RD 推程式碼到 Gitlab Server，觸發 Gitlab CI Runner。

2. GitLab CI 自動完成單元測試、弱掃、並打包 Image 推送到 Registry。

3. GitLab CI 修改 K8S 配置的 YAML 檔案，並推送到 Gitlab Server 的 K8S 配置 Repo。
    - 建議在 CI 腳本中使用 `kustomize` 或 `yq` 這類專門處理 YAML 的工具，而不是直接用 sed 替換字串。這樣可以確保 YAML 格式不會因為自動化修改而跑掉。
    - GitLab CI 要修改 K8S 配置 Repo，需要一個可以 `write_repository` 權限的 Token。

4. Gitlab Server 接收到新的 K8S配置Repo 的更新，觸發 Webhook，通知 ArgoCD 服務。
    - 記得在 GitLab 的 Webhook 設定中，勾選 "Push events"。另外，ArgoCD 的 Webhook URL 通常長這樣：https://<argocd-url>/api/webhook。

5. ArgoCD: 收到通知，比對發現 Out of Sync，指揮 K8s 執行滾動更新。

6. K8s: 從 Registry 拉取新 Image，完成部署。
    - 如果 K8s 叢集很大，建議開啟 ArgoCD 的 "Self-Heal"（自我修復）功能。這樣即使有人手動去 K8s 亂改東西，ArgoCD 也會自動把它校正回 Git 上的版本。