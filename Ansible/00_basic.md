# Ansible 特性

* 基於 Python 語言實現。

* 部署簡單，基於 python 和 SSH (預設已安裝)、不須額外安裝 agent。

* 安全性: 基於 OpenSSH。

* 模塊化: 內建幾千個模塊，通過調用指定模塊，完成特定任務。

    * 支援自定義模塊，可使用任何程式語言寫模塊。

    * 包含核心的三個組件: 
        1. Paramiko (python 對 SSH 的實現)
        2. PyYAML
        3. Jinja2(模板引擎)

* 冪等性(Idempotent): 

    * 一個任務執行1次和執行n次的效果一樣，防止重複執行。

* 支持 Playbook 編排任務、YAML格式、各種數據結構。

* 支持 Ansible Role。


<br/>

<br/>

# 架構圖

<img src='../_image/ansible-architecture.png'>

<br/>



中間黑框的就是 ansible，包含以下四種組件。

* `Inventory` : 被管理的主機，都會記錄在此清單中。

* `API` : 某些自動化工具，像是 gitlab CI/CD，會透過 ansible 的 API 來觸發執行 ansible 的模塊命令，故 ansible 有提供 API。

* `Modules` : 核心功能單元，用於執行具體任務（如安裝軟體、管理文件、配置系統）。

* `Plugins` : 額外的插件。

ansible 是通過 SSH 協議來向 Hosts (目標主機) 來進行操作。