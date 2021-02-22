#Linux 

# 安裝

```
yum install -y mysql mysql-server
mysql_secure_installation
```

# 測試是否能使用root帳號登入資料庫

這一步是要確認 DB已經是啟動了也可以連線了

`mysql -u root -p`
如果順利登錄就沒問題了

看一下現在有哪些資料庫
`show databases;`

離開資料庫
`quit`

# 移除
```
yum remove -y $(rpm -qa | grep mysql)
rm -rf /var/lib/mysql
mkdir /var/lib/mysql
chown -R mysql.mysql /var/lib/mysql
```