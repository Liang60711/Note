## 概述

Roles 是用來組織和重用 Playbook 的一種方式。通過將任務、變數、模板、檔案等分門別類，Roles 能使 Playbook 結構更清晰、更易於維護，並提高可重用性。

<br/>

<br/>

## 目錄結構

```sh
my_role/             # Role 的名稱
│
├── tasks/           # 任務列表 (main.yml)
│   └── main.yml
│
├── handlers/        # 處理器列表 (main.yml)
│   └── main.yml
│
├── defaults/        # 預設變數 (main.yml)
│   └── main.yml
│
├── vars/            # 角色變數 (main.yml)
│   └── main.yml
│
├── files/           # 靜態檔案 (複製到遠端)
│   └── example.conf
│
├── templates/       # Jinja2 模板檔案
│   └── example.conf.j2
│
├── meta/            # 角色的元數據 (dependencies 等)
│   └── main.yml
│
└── README.md        # 角色說明檔案
```

`tasks/main.yml`: 包含 Role 的主要任務。

`handlers/main.yml` : 定義處理器 (Handlers)，通常用來處理需要重啟的服務。

`defaults/main.yml` : 定義預設變數，這些變數可以被覆蓋。

`vars/main.yml` : 定義角色中的變數，優先級高於 defaults。

`files/` : 包含需要傳輸到遠端主機的靜態檔案。例如 nginx.conf。

`templates/` : 包含 Jinja2 模板檔案，可以動態渲染內容。例如 nginx.conf.j2。

`meta/main.yml` : 包含角色的元數據，例如依賴角色。

<br/>

<br/>

執行順序: 

各目錄下的 `main.yml` 只是一個入口，需要在其中 include 其他配置。

```
my_role/
│
└── tasks/
    ├── main.yml
    ├── install.yml
    ├── config.yml
    ├── index.yml
    └── service.yml
```

`main.yml` 要依照執行順序去 include

```yml
# main.yml
- include: install.yml
- include: config.yml
- include: index.yml
- include: service.yml
```



<br/>

<br/>

## 建立一個 Role

使用 ansible-galaxy 腳本，快速生成一個專案。

```sh
ansible-galaxy init my_role
```

<br/>

<br/>

## 調用 Roles

在 Playbook 中使用角色

```yml
- name: Example of using roles
  hosts: all
  roles:
    - role: my_role    # 使用 Role
      nginx_port: 8080 # 可傳遞變數
```

<br/>

<br/>

## 下載 Roles
使用公開的第三方角色，可以透過 ansible-galaxy 下載。

```yml
ansible-galaxy install geerlingguy.nginx
```

下載的第三方 Role 會儲存在 `~/.ansible/roles/` 路徑下。