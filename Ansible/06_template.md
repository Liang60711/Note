## 模板

1. 使用 python jinja2 模板引擎。

2. 可以支持許多基本語法

    * 插入變數

    * 條件判斷

    * 迴圈

    * 過濾器

<br/>

<br/>

## 基本使用

目錄結構

```
ansible_project/
├── playbook.yml
├── templates/
│   └── nginx.conf.j2
└── vars/
    └── vars.yml
```



先定義好模板 `nginx.conf.j2`，副檔名限制是 `j2`

```
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};

    location / {
        proxy_pass http://{{ backend_server }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

在 playbook 使用模板

```yml
- hosts: localhost
  # 變數在這邊都必須要填上
  vars:
    nginx_port: 80
    server_name: "example.com"
    backend_server: "127.0.0.1:8080"
  tasks:
    - name: Generate Nginx configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf

```