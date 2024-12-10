## playbook 概念

1. YAML 格式：Playbook 是用 YAML 格式編寫。

2. 名詞定義

    * Playbook: 是 Ansible 的核心執行單位，由一個或多個 Play 組成的集合。

    * Play: 指在 Playbook 中，用於描述對一組目標主機執行一系列任務的最小單位。


3. 基本結構

    * `name` : 提供任務或 Play 的描述，便於理解和排錯。

    * `hosts` : 定義此 Play 適用的目標主機組，可以是單一主機或主機組。

    * `become` : 是否需要提升權限（例如切換到 root 使用者）。

    * `tasks` : 任務清單，每個任務定義要執行的操作，例如安裝軟體、複製檔案或配置服務。

    * `模組` : Ansible 提供的功能模組，執行具體的操作，如: yum、service

    ```yaml
    - name: 安裝與配置 Nginx
      hosts: uatservers # 可以是組別，也可以是單一主機
      become: yes  # 是否需要提升權限(提升為 root 權限執行)
      tasks: # 任務清單，每個任務定義要執行的操作
        - name: 安裝 Nginx
          yum:
            name: nginx
            state: present

        - name: 啟用並啟動 Nginx 服務
          service:
            name: nginx
            state: started
            enabled: yes

        # 也可以這樣寫，用了 ansible -a 的寫法
        - name: 啟用並啟動 Nginx 服務
          service: name=nginx state=started enabled=yes
    ```

<br/>

<br/>

## playbook 命令

格式

```sh
ansible-playbook xxxxxx.yml [options]
```

<br/>

### 常見 options

-i, --inventory
指定清單文件或主機清單
```sh
ansible-playbook -i inventory.ini playbook.yml
```

--list-hosts
(常用)列出將要運行的主機，並不執行實際任務。
```sh
ansible-playbook --list-hosts playbook.yml
```

--list-tasks
(常用)列出將要運行任務。
```sh
ansible-playbook --list-tasks playbook.yml
```

--list-tags
列出tags。
```sh
ansible-playbook --list-tags playbook.yml
```


--check
在不實際執行更改的情況下運行。
```sh
ansible-playbook --check playbook.yml
```

--timeout
設置連接超時時間。
```sh
ansible-playbook --timeout=30 playbook.yml
```

-v, --verbose
增加輸出詳細程度，使用 -vvv 或更多 v 來進一步詳細化。
```sh
ansible-playbook -vvv playbook.yml
```

--limit
只針對主機列表中的主機執行，會覆蓋 playbook.yml 的 hosts 屬性。
```sh
absible-playbook --limit 10.10.30.180 playbook.yml
```

例如，playbook.yml 中的 hosts 是寫 optservers 群組，但是這個群組底下沒有 10.10.30.180 時，就不會運行，原因是 --limit 只會去篩選 optservers 中的某些主機運行。

<br/>


--start-at-task
從指定的任務名稱開始執行，這對於重試某個特定任務很有用。
```sh
ansible-playbook --start-at-task "Install nginx" playbook.yml
```

--become
啟用 become 模式，以便以其他用戶身份執行任務（通常是 root）。
```sh
ansible-playbook --become playbook.yml
```

<br/>

<br/>

## 舉例

安裝 MySQL 的 playbook

```yaml
- name: Install and configure MySQL
  hosts: uatservers
  remote_user: root
  tasks:
    - name: install MySQL repository
      yum: 
        name: https://repo.mysql.com/mysql80-community-release-el8.rpm
        state: present
    - name : Install MySQL server
      yum:
        name: mysql-server
        state: present
    - name: Ensure MySQL is started and enabled
      service:
        name: mysqld
        state: started
        enabled: true
    # 執行 MySQL 的安全性設置
    - name: Secure MySQL installation
      expect:
        command: mysql_secure_installation
        responses:
          'Enter current password for root (enter for none):': ''
          'Set root password? \[Y/n\]': 'Y'
          'New password:': 'YourRootPassword'
          'Re-enter new password:': 'YourRootPassword'
          'Remove anonymous users? \[Y/n\]': 'Y'
          'Disallow root login remotely? \[Y/n\]': 'Y'
          'Remove test database and access to it? \[Y/n\]': 'Y'
          'Reload privilege tables now? \[Y/n\]': 'Y'
```