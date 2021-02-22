#Linux 

# 為何使用 MariaDB

MariaDB 適合資料庫新手學習，他是 MySQL 的分支，還有他是免費開源的，非常適合新手再上面建立各種 table, view 練習

缺點：不適合用於建立大型專案；如果需要應用在大型網站，維護巨量資料的專案，使用 NoSQL，利用 RESTful Web Sevice 去取資料是不錯的選擇

PostgreSQL - 最快最好的資料庫軟體，但很多人在上面摔跤


# 安裝
```
yum install mariadb mariadb-server php-mysqlnd php-pdo -y
mysql_secure_installation
```
進入 mariadb 的安裝精靈

# 測試是否能使用root帳號登入資料庫

這一步是要確認 DB已經是啟動了也可以連線了

`mysql -u root -p`
如果順利登錄就沒問題了

看一下現在有哪些資料庫
`show databases;`

離開資料庫
`quit`


---
# 同場加映

但是資料庫還是要做些設定的
1. 資料庫中文變亂碼->檢查你的資料庫設定檔編碼將他改成 utf-8
2. 請建立一個新的使用者帳號，我們儘量不要使用 root 最高管理者權限去操作，未來將資料庫上線後這一組將成為我們要使用的帳號，避免讓駭客直接掌控了妳所有的資料庫
3. 請為這個新建帳號給予他可以使用的資料庫權限

## 第一步：建立資料庫

以一個會員範例來說，我們要有一個好的識別度名稱給這個資料庫
`CREATE DATABASE MEMBER`

## 第二步：給這個資料庫一個使用者

建立一個使用者，登入的密碼為123456

> 注意!!!請千萬不要使用	範例這種密碼,只要在[這裡](https://13639-presscdn-0-80-pagely.netdna-ssl.com/wp-content/uploads/2017/12/Top-100-Worst-Passwords-of-2017a.pdf )上榜的密碼都是高風險群

`CREATE USER 'T00001'@'localhost' IDENTIFIED BY '123456';`

賦予 T00001 MEMBER 資料庫所有權限，只限於 localhost 本機 IP 使用，你可以將 localhost 替換成你要開給哪個 IP 使用，之後要重新整理權限

```
  1  GRANT ALL PRIVILEGES ON MEMBER.* TO 'T00001'@'localhost';
  2  FLUSH PRIVILEGES;
  3  quit	# 離開資料庫

```

使用 T00001 登入本機資料庫，-p 參數需要輸入密碼
之後會要求你輸入密碼

`mysql -u T00001 -p`

如果順利登錄就沒問題了

# 安裝 phpMyAdmin

找到適用的版本
![](img/Pasted%20image%2020201207140849.png)
下載壓縮檔
`curl -L "https://files.phpmyadmin.net/phpMyAdmin/5.0.4/phpMyAdmin-5.0.4-all-languages.zip" -o /usr/share/nginx/html/phpMyAdmin.zip`
解壓縮並更名
`unzip phpMyAdmin.zip && mv phpMyAdmin-5.0.4-all-languages/ /usr/share/nginx/html/phpMyAdmin`
`restorecon -Rvv /usr/share/nginx/html/phpMyAdmin`
`chown -R nginx:nginx /usr/share/nginx/html/phpMyAdmin`

測試
![](img/Pasted%20image%2020201207145038.png)

## 故障排除
### 需要在設定檔內設定密碼
![](img/Pasted%20image%2020201207145132.png)
主要設定檔是 `/usr/share/nginx/html/phpMyAdmin/config.inc.php`
主要設定檔範例是 `./phpMyAdmin/config.sample.inc.php`
`cp /usr/share/nginx/html/phpMyAdmin/config.sample.inc.php /usr/share/nginx/html/phpMyAdmin/config.inc.php`
第 18 行 ![](img/Pasted%20image%2020201207150709.png)
重新整理，錯誤訊息已經消失了

### 缺少 /usr/share/nginx/html/phpMyAdmin/tmp/ 目錄
![](img/Pasted%20image%2020201207145140.png)
更改 SELinux 的設定
```
semanage fcontext -at httpd_tmp_t '/usr/share/nginx/html/phpMyAdmin/tmp(/.*)?'
restorecon -Rvv /usr/share/nginx/html/phpMyAdmin/tmp/
```
重新整理，錯誤訊息已經消失了
