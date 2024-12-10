## handler / notify

用來實現條件性任務執行，兩個屬性是綁定使用的。


```yaml
- hosts: uatservers
  remote_user: root
  tasks:
    - name: Install nginx
      yum: 
        name: nginx
        state: present
    # 如果不寫 handler，就無法重啟 nginx
    - name: Update nginx configuration file
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf
      notify: restart nginx  # 通知 handler "restart nginx"
    
    - name: Start nginx
      service: 
        name: nginx
        state: started
        enabled: yes

  # 和 tasks 是同一層
  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

* 如果 copy 任務發生變更，會觸發 restart nginx 的 handler。
* 如果配置文件未發生變更，restart nginx 不會執行。

* handlers 和 notify 都可以寫多個服務。

    ```YAML
    ####################
    notify: 
      - Restart nginx
      - Check nginx process
    
    ####################
    handlers:
      - name: Restart nginx
        service: name=nginx state=restarted enabled=yes
      - name: Check nginx process
        shell: killall -0 nginx &> /tmp/nginx.log
    ```

<br/>

<br/>

## tags

可以將指定的 tasks、roles 或部分內容加上 tags；之後就可以指定要執行的 tags，不至於全部 tasks 都執行。

tags 和 handlers 不衝突，可以一併使用。

```yaml
- hosts: uatservers
  remote_user: root
  tasks:
    - name: Install nginx
      yum: 
        name: nginx
        state: present
      tags: # 標籤定義
        - install
        - nginx
```

在執行 playbook 時，可以先查看標籤

```sh
ansible-playbook playbook.yml --list-tags
```



只執行特定標籤的任務

```sh
ansible-playbook playbook.yml --tags "install"
```

執行多個標籤的任務

```sh
ansible-playbook playbook.yml --tags "install,nginx"
```

跳過特定標籤的任務

```sh
ansible-playbook playbook.yml --skip-tags "install"
```

<br/>

<br/>


## 變數

* 變數只可以字母開頭，不可使用數字開頭。

* 變數分為全域變數、角色變數

* 變數的權重(由低到高)，概念就是`越特定配置的，優先級越高`

    1. 預設變數（defaults/main.yml）

    2. 角色變數（vars/main.yml）

    3. Playbook 變數（vars 部分）

    4. 主機變數（host_vars 或 group_vars）

        ```yml
        # hosts 配置檔案中定義
        [webservers]
        server1 ansible_host=192.168.1.10 ansible_user=admin app_env=production
        server2 ansible_host=192.168.1.11 ansible_user=admin app_env=staging
        ```

    5. 命令行變數（使用 -e 參數傳遞）

        ```sh
        ansible-playbook playbook.yml -e "var1=user1"
        ```

    6. 事前設定的事物（例如，Facts）

    7. 模板和 Jinja2 表達式 中的變數引用


* 調用方式，使用`雙大括號`

    ```yaml
    # 使用 playbook 調用
    - hosts: uatservers
      remote_user: {{ vars_user }}
    ```

* 變數類型

    * 字串

        ```yaml
        vars:
          app_name: "uatservers"
        ```

    * 列表

        ```yaml
        vars:
          packages:
            - nginx
            - git
            - vim
        ```
    
    * 字典

        ```yaml
        vars:
          db_config:
            user: "admin"
            password: "secret"
            host: "db.example.com"
        ```

<br/>

<br/>

## 變數文件

可以在一個獨立的 playbook 文件中，定義變數，在另一個執行的 playbook 文件來飲用此變數文件，


先定義一個 vars.yml 文件

```yml
# vars.yaml
package_name: vsftpd
service_name: vsftpd
```

執行文件 app.yml

```yml
# app.yaml
- hosts: appservers
  remote_user: root
  vars_files:
    - vars.yml
  tasks:
    - name: install package
      yum: name={{ package_name }}
      tags: install
    - name: start service
      service: name={{ service_name }} state=started  enabled=yes
```

<br/>

<br/>

## gather_facts

用來收集遠端主機的系統資訊的一項功能。這些資訊包括主機的操作系統、IP 地址、磁碟資訊、內存大小等，這些事實（facts）可以用於 Playbook 中來動態配置任務。

`預設是開啟`的，如果要禁用，可設為 `no`

```yaml
- name: No facts gathering example
  hosts: all
  gather_facts: no
  tasks:
    - name: Run a simple command
      command: echo "Hello, world!"
```