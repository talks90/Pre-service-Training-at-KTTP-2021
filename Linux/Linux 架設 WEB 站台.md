# 基本環境建置
 平台 - Linux

## 先置作業
確認網路卡設定
`ip addr show`
![](img/Pasted%20image%2020201207091319.png)
確認網路狀況
`ping 1.1.1.1`
![](img/Pasted%20image%2020201207091431.png)
確認 epel-release 延伸套件安裝狀況
`yum install epel-release`
>  root 身分才能執行這個動作

![](img/Pasted%20image%2020201207092012.png)

更新 yum
`yum upgrade`
>  root 身分才能執行這個動作

![](img/Pasted%20image%2020201207092537.png)
![](img/Pasted%20image%2020201207092549.png)
![](img/Pasted%20image%2020201207092601.png)
![](img/Pasted%20image%2020201207092609.png)
![](img/Pasted%20image%2020201207092619.png)
![](img/Pasted%20image%2020201207092627.png)

如果 kernel 有更新，需要重新開機
`reboot`

### Web Server
- engine
	- nginx - [[Linux/Linux nginx 安裝]]
	- Apache - 因為太肥大了，所以不教
- php - 一種程式語言，讓 html 的語言更生動
	- 使用模組 - php-fpm - [[Linux/Linux php-fpm 安裝]]
- SQL
	- Mariadb[[Linux/Linux mariaDB 安裝]]
	- MySQL [[Linux/Linux MySQL 安裝]]
### DNS 名稱
- Bind [[Linux/Linux DNS 架設]]

## 應用程式平台
到目前為止，我們的站台架構就是所謂的 LNPM (Linux + Nginx + Php moudle + Mariadb) 架構

台灣也很多廠商提供這類的服務，例如：架一個 Wordpress 的站台收費大約是 1000 元/月，但需要換版或增加功能時都要再另外收費

Php moudle 的部分可以有很多種選擇，如：
- Joomla - 推薦，但是要更新
- Wordpress - 只要不更新，駭客很喜歡攻擊
- Open Cart - Open Source 的購物車；再接物流、金流
- Drupal - 是相對安全的 CMS （Content Mangement System ，內容管理系統 ），也可以接物流、金流 [[Linux/Linux Drupal 安裝]]

## 安全
為了安全，我們不要將檔案放在預設的位置，會讓有心人士太好猜到你的網站架構
## 移動 myPhpAdmin 的位置
移動資料夾
`mv /usr/share/nginx/html/phpMyAdmin ../`
更改 Nginx 設定檔裡的 root 資料夾位置
`vim /etc/nginx/conf.d/mysql.conf`
![](img/Pasted%20image%2020201216131227.png)
重置 Nginx 設定檔
`nginx -s reload`
站台正常囉！
![](img/Pasted%20image%2020201216131548.png)
## 移動 WWW 的位置
步驟同上喔！


### Firewall
### SELinux
要注意扮演什麼角色、使用什麼合法的 process、操作什麼檔案類型

1. 安裝 setroubleshooting 套件，它會把錯誤訊息存到 /var/log/message
2. 安裝 semanage 套件，可以針對內容 fcontext、或通訊埠號 port 做設定，restorecon 將目錄內的所有檔案改成該目錄的權限
3. setsebool - 設定程序可以做哪些動作 getsebool
4. setenforce 強制關掉，但要重新開機；看目前的狀況 getenforce，設定會存在 /etc/sysconfig/selinux