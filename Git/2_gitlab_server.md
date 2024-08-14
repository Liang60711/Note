# 安裝

```sh
# 0 sudo
sudo -i

# 1 更新 yum
yum update

# 2 安裝依賴包
yum install -y curl policycoreutils openssh-server

# 3 啟用服務
systemctl enable sshd
systemctl start sshd

# 4 安裝發送郵件套件 (可選)
yum install -y postfix
systemctl enable postfix
systemctl start postfix

# 5 添加官方 gitlab 倉庫
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

# 6 指定 domain 名稱，並安裝 gitlab
EXTERNAL_URL="http://your_domain_or_ip" yum install -y gitlab-ce

# 7 查看配置 / 刷新配置
vim gitlab-ctl
gitlab-ctl reconfigure

# 8 防火牆設置 需打開 80/443
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload

# 9 啟用服務 / 重啟服務
gitlab-ctl start
gitlab-ctl restart

# 10 查看服務狀態
gitlab-ctl status
```

以上完成安裝後，可以訪問 `http://your_domain_or_ip`

管理員密碼會在以下檔案：
```
vim /etc/gitlab/initial_root_password
```

<br/>

<br/>


# LDAP 配置
配置，需要用一組 ldap 的用戶作為發起查詢，所以需要一組 bind_dn/password 的帳號
```sh
gitlab_rails['ldap_enabled'] = true
gitlab_rails['ldap_servers'] = YAML.load <<-EOS
main:
  label: 'LDAP'
  host: 'dc2016.hpicorp.com.tw'
  port: 389
  uid: 'sAMAccountName'
  bind_dn: 'CN=ricoliang,OU=軟體開發部,OU=資訊處,OU=HPICORP,DC=hpicorp,DC=com,DC=tw'
  password: 'xxxxxxxx'
  encryption: 'plain' # "start_tls" or "simple_tls" or "plain"
  verify_certificates: false
  active_directory: true
  smartcard_auth: false
  allow_username_or_email_login: false
  lowercase_usernames: false
  block_auto_created_users: false
  base: 'OU=HPICORP,DC=hpicorp,DC=com,DC=tw'
  user_filter: 'ou=資訊處'
EOS
```

配置完成後，記得刷新 / 測試

```sh
gitlab-ctl reconfigure # 刷新

gitlab-rake gitlab:ldap:check # 連接測試
```