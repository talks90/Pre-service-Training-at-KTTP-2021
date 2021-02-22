# 題目
[題目 PDF](img/11_T5.7.1伺服器環境規劃建置__Linux(題目版).pdf)

1.在 Vmware Ｗorkstation 上，新增一個 VM，其內容需包含：硬碟空間為 20GB，8GB 記憶體以及可上網際網路的網路卡一張
2.到 http://192.168.1.1/pub/ 下載 CentOS7.iso 進行安裝
3.安裝要求：
- /boot 目錄空間 2G，使用 xfs 檔案系統格式
- Swap 空間 4G，使用 swap 格式
- / 空間 14G，使用 xfs 檔案系統格式
- 開啟 SELinux 設定
- 開啟防火牆！配合後來命題，開通防火牆設定
4.設定網路，可連上外部網頁
5.安裝 epel-release 套件
6.安裝 MariaDB 套件，設定 root 密碼為「1qaz2wsx」，並設定預設開機啟動
7.安裝 Apache 套件，並且設定預設開機啟動
8.安裝 php 語言套件
9.安裝 Drupal 7.0 系列的 CMS 套件；過程之中，可能會發生許多故障問題，請將其排除。管理者密碼設定成「1qaz2wsx」

# 解題步驟
## 在 Vmware Ｗorkstation 上，新增一個 VM，其內容需包含：硬碟空間為 20GB，8GB 記憶體以及可上網際網路的網路卡一張
![](img/Pasted%20image%2020210106083016.png)
![](img/Pasted%20image%2020210106083040.png)
![](img/Pasted%20image%2020210106083105.png) 
這裡選項最多到 CentOS7 64-bit，沒關係，就選它吧！
![](img/Pasted%20image%2020210106083309.png)
照慣例幫虛擬機取個名字吧
![](img/Pasted%20image%2020210106083805.png)
硬碟空間設 20GB，檔案設為單一檔案
![](img/Pasted%20image%2020210106084019.png)
調整一下記憶體，確定有裝網路卡 (NAT)
![](img/Pasted%20image%2020210106084157.png) ![](img/Pasted%20image%2020210106085428.png) ![](img/Pasted%20image%20202101060854281.png)

## 到 http://192.168.1.1/pub/ 下載 CentOS7.iso 進行安裝
教學改採用 CentOS8，下載點為：
- [教室 FTP](ftp://10.10.53.228) ![](img/Pasted%20image%2020210107083728.png)
- [官方下載點](https://wiki.centos.org/zh-tw/Download) ![](img/Pasted%20image%2020210107083828.png)

這裡用教室 FTP 的 **CentOS-8.2.2004-x86_64-dvd1.iso** 來做，先下載到自己的電腦
![](img/Pasted%20image%2020210107084007.png)
把 ISO 檔掛載到虛擬機上
![](img/Pasted%20image%2020210107084825.png) ![](img/Pasted%20image%2020210107085009.png)
開機進行安裝
![](img/Pasted%20image%2020210107085048.png) ![](img/Pasted%20image%2020210107085120.png)
選擇安裝期間使用的語言，這裡選擇**繁體中文**
![](img/Pasted%20image%2020210107094044.png)
鍵盤配置
![](img/Pasted%20image%2020210107094010.png) ![](img/Pasted%20image%2020210107101007.png)
時區選台北
![](img/Pasted%20image%2020210107101439.png) ![](img/Pasted%20image%2020210107101343.png)

## /boot 目錄空間 2G，使用 xfs 檔案系統格式
![](img/Pasted%20image%2020210107101439-1.png) ![](img/Pasted%20image%2020210107102642.png) ![](img/Pasted%20image%2020210107103104.png) ![](img/Pasted%20image%2020210107103147.png) ![](img/Pasted%20image%2020210107103217.png)
## Swap 空間 4G，使用 swap 格式
![](img/Pasted%20image%2020210107133308.png) ![](img/Pasted%20image%2020210107133336.png)
## / 空間 14G，使用 xfs 檔案系統格式
![](img/Pasted%20image%2020210107133418.png) ![](img/Pasted%20image%2020210107133445.png)

接受設定，開始安裝
![](img/Pasted%20image%2020210107133509.png) ![](img/Pasted%20image%2020210107133523.png) ![](img/Pasted%20image%2020210107133638.png)

設定 root 密碼
![](img/Pasted%20image%2020210107133745.png) ![](img/Pasted%20image%2020210107133804.png)

創建用戶
![](img/Pasted%20image%2020210107133922.png) ![](img/Pasted%20image%2020210107134146.png) ![](img/Pasted%20image%2020210107134507.png)

初始設定
![](img/Pasted%20image%2020210107134649.png) ![](img/Pasted%20image%2020210107134706.png) ![](img/Pasted%20image%2020210107134747.png) ![](img/Pasted%20image%2020210107134833.png) ![](img/Pasted%20image%2020210107134843.png) ![](img/Pasted%20image%2020210107134908.png) ![](img/Pasted%20image%2020210107134933.png) ![](img/Pasted%20image%2020210107134944.png)

## 開啟 SELinux 設定
SELinux 在 CentOS 裡有內建了，可以開終端機用 `ls -Z` 檢查一下
![](img/Pasted%20image%2020210108083637.png)

## 開啟防火牆！配合後來命題，開通防火牆設定
防火牆在 CentOS 裡有內建了，可以開終端機用 `systemctl status firewalld` 檢查一下
![](img/Pasted%20image%2020210108104251.png)

## 設定網路，可連上外部網頁
![](img/Pasted%20image%2020210108083143.png) ![](img/Pasted%20image%2020210108083228.png)

更新 CentOS；開終端機下 `su` 指令，輸入密碼後切換為 root，再下 `yum upgrade -y` 指令
![](img/Pasted%20image%2020210108091918.png) 
更新過程 Kernel 有做更新，所以將機器重新啟動；在終端機下 `reboot` 指令
![](img/Pasted%20image%2020210108093332.png) ![](img/Pasted%20image%2020210108093619.png)

## 安裝 epel-release 套件
安裝 epel-release 套件；開終端機下 `su` 指令，輸入密碼後切換為 root，再下 `yum install epel-release -y` 指令
![](img/Pasted%20image%2020210108100957.png) ![](img/Pasted%20image%2020210108101015.png)

## 安裝 MariaDB 套件，設定 root 密碼為「1qaz2wsx」，並設定預設開機啟動
安裝 MariaDB 套件；開終端機下 `yum install mariadb mariadb-server php-mysqlnd php-pdo -y` 指令
![](img/Pasted%20image%2020210108101956.png) ![](img/Pasted%20image%2020210108102721.png)
啟動 MariaDB 服務，並設定預設開機啟動；在終端機下 `systemctl enable --now mariadb` 指令
![](img/Pasted%20image%2020210108111345.png)
檢查 MariaDB 服務；在終端機下 `systemctl status mariadb` 指令，可以看到目前服務已經在執行了，且預設開機啟動
![](img/Pasted%20image%2020210108130934.png)
執行 mariadb 安裝精靈；在終端機下 `mysql_secure_installation` 指令
![](img/Pasted%20image%2020210108102920.png)
第一次安裝 root 密碼預設為**空白**；直接按 **Enter**
![](img/Pasted%20image%2020210108103005.png)
是否要設定 root 密碼，題目要求要設定；按 **y** 或直接按 **Enter**
![](img/Pasted%20image%2020210108111616.png)
輸入 2 次密碼
![](img/Pasted%20image%2020210108111753.png)
其他設定都直接用預設值就好；直接按 **Enter**
![](img/Pasted%20image%2020210108111902.png) ![](img/Pasted%20image%2020210108111911.png) ![](img/Pasted%20image%2020210108111921.png) ![](img/Pasted%20image%2020210108111933.png)
完成安裝
![](img/Pasted%20image%2020210108111954.png)
登入 MariaDB；在終端機下 `mysql -u root -p` 指令
![](img/Pasted%20image%2020210108142130.png)
輸入 root 密碼 **1qaz2wsx**
![](img/Pasted%20image%2020210108142214.png) ![](img/Pasted%20image%2020210108142238.png)
> 小提醒，在資料庫裡下的指令，除了 quit 之外最後面都要加上 **;**

建立 Drupal 用的資料庫；在 MariaDB 下 `CREATE DATABASE drupal;` 指令，drupal 是資料庫名稱
![](img/Pasted%20image%2020210108142622.png)
建立 Drupal 用的使用者；在 MariaDB 下 `CREATE USER 'drupal'@'%' IDENTIFIED BY '1qaz2wsx';` 指令，這裡的 drupal 是使用者名稱，1qaz2wsx 是密碼
![](img/Pasted%20image%2020210108143731.png)
把 drupal 資料庫的權限開放給 drupal 使用者；在 MariaDB 下 `grant all privileges on drupal.* to 'drupal'@'%';` 指令
![](img/Pasted%20image%2020210108143841.png)
重置資料庫的權限；在 MariaDB 下 `FLUSH PRIVILEGES;` 指令
![](img/Pasted%20image%2020210108144226.png)
檢查一下資料庫；在 MariaDB 下 `show databases;` 指令
![](img/Pasted%20image%2020210108144327.png)
退出 MariaDB ；在 MariaDB 下 `quit` 指令
![](img/Pasted%20image%2020210108144536.png)

## 安裝 Apache 套件，並且設定預設開機啟動
教學改採用 Nginx，改安裝 Nginx 套件；開終端機下 `yum install nginx -y` 指令
![](img/Pasted%20image%2020210108104651.png) ![](img/Pasted%20image%2020210108104703.png)
打開防火牆 http 通訊埠；在終端機下 `firewall-cmd --add-service=http` 指令
![](img/Pasted%20image%2020210108105038.png)
確認防火牆是否開啟；在終端機下 `firewall-cmd --zone=public --list-all` 指令
![](img/Pasted%20image%2020210108105237.png)
將現在的防火牆設定永久保存；在終端機下 `firewall-cmd --runtime-to-permanent` 指令
![](img/Pasted%20image%2020210108105404.png)
將 Nginx 設定為預設開機啟動；在終端機下 `systemctl enable nginx` 指令
![](img/Pasted%20image%2020210108110106.png)
啟動 Nginx 服務；在終端機下 `systemctl start nginx` 指令
![](img/Pasted%20image%2020210108110235.png)
檢查 Nginx 服務；在終端機下 `systemctl status nginx` 指令，可以看到目前服務已經在執行了，且預設開機啟動
![](img/Pasted%20image%2020210108110524.png)

## 安裝 php 語言套件
安裝 php 語言套件；開終端機下 `yum install php php-fpm php-json php-gd php-xml php-mbstring php-opcache -y` 指令
![](img/Pasted%20image%2020210111134957.png) ![](img/Pasted%20image%2020210108131743.png)
修改 php-fpm 設定檔；在終端機下 `vim /etc/php-fpm.d/www.conf` 指令
![](img/Pasted%20image%2020210108131355.png)
如下圖示修改第 22, 26,64 行的值
![](img/Pasted%20image%2020210108132132.png) ![](img/Pasted%20image%2020210108132256.png)
修改 php session檔案權限；在終端機下 `su` 指令，輸入密碼後切換為 root，再下 `chown nginx:nginx /var/lib/php/session` 指令
![](img/Pasted%20image%2020210108134456.png)
啟動 php-fpm 服務；在終端機下 `systemctl enable --now php-fpm` 指令
![](img/Pasted%20image%2020210108140301.png)
檢查 php-fpm 服務；在終端機下 `systemctl status php-fpm` 指令，可以看到目前服務已經在執行了，且預設開機啟動
![](img/Pasted%20image%2020210108140827.png)
重新繫結 SELinux 設定；在終端機下 `restorecon -Rvv /usr/share/nginx/html/` 指令
![](img/Pasted%20image%2020210108141241.png)
檢查 Nginx 設定檔；在終端機下 `nginx -t` 指令
![](img/Pasted%20image%2020210108141334.png)
重新載入 Nginx 設定檔；在終端機下 `nginx -s reload` 指令
![](img/Pasted%20image%2020210108141413.png)

## 安裝 Drupal 7.0 系列的 CMS 套件；過程之中，可能會發生許多故障問題，請將其排除。管理者密碼設定成「1qaz2wsx」
教學改採用 Drupal 8.9.11，下載安裝檔；開瀏覽器連到 `https://www.drupal.org/project/drupal/releases/8.9.11` 網址
![](img/Pasted%20image%2020210108145411.png) ![](img/Pasted%20image%2020210108145448.png)
把安裝檔解壓縮
![](img/Pasted%20image%2020210108145621.png) ![](img/Pasted%20image%2020210108145629.png)
把資料夾更名為 drupal
![](img/Pasted%20image%2020210108145930.png) ![](img/Pasted%20image%2020210108150005.png)
建立 /usr/share/nginx/www/ 資料夾後再把 drupal 資料夾移到它之下；開終端機下 `su` 指令，輸入密碼後切換為 root，再下 `mkdir /usr/share/nginx/www && mv /home/test/下載/drupal /usr/share/nginx/www/drupal` 指令
![](img/Pasted%20image%2020210108151341.png)
下載正體中文語言包；開瀏覽器連到 `https://localize.drupal.org/download` 網址
![](img/Pasted%20image%2020210108152129.png) ![](img/Pasted%20image%2020210108152158.png) ![](img/Pasted%20image%2020210108152222.png)
建立/usr/share/nginx/www/drupal/sites/default/files/translations 資料夾後再把 drupal 中文語言包移到它之下；開終端機下 `su` 指令，輸入密碼後切換為 root，再下 `mkdir -p /usr/share/nginx/www/drupal/sites/default/files/translations && mv /home/test/下載/drupal-8.9.12.zh-hant.po /usr/share/nginx/www/drupal/sites/default/files/translations` 指令
![](img/Pasted%20image%2020210111125250.png)
將 Nginx 初始的 default_server 設定關掉，避免 Drupal 的設定衝突；在終端機下 `vim /etc/nginx/nginx.conf` 指令
![](img/Pasted%20image%2020210111125749.png)
如下圖示修改第 38-57 行，在行首加上 **\#** 註解符號
![](img/Pasted%20image%2020210111125946.png)
新增 Nginx for Drupal 的設定檔；在終端機下 `vim /etc/nginx/conf.d/www.conf` 指令
![](img/Pasted%20image%2020210111130030.png)
如下圖示，編輯**設定檔內容**
```
server {
    listen       80;
    server_name  _;

    root         (drupal 目錄放置的位址);
    index index.php;

	error_log /var/log/nginx/error.log;
	access_log /var/log/nginx/access.log main;
	location / {
		try_files $uri /index.php?$query_string;
	}
	include /etc/nginx/default.d/*.conf;
}
```
![](img/Pasted%20image%2020210111130202.png)
重置 Nginx 設定檔；在終端機下 `nginx -s reload` 指令
![](img/Pasted%20image%2020210108141413.png)
複製 Drupal 的設定程式；在終端機下 `cp /usr/share/nginx/www/drupal/sites/default/default.settings.php /usr/share/nginx/www/drupal/sites/default/settings.php`
![](img/Pasted%20image%2020210111131512.png)
設定期間需要 ./sites/default/settings.php 及 ./sites/default/files 的讀寫權限；在終端機下 `chmod 777 /usr/share/nginx/www/drupal/sites/default/settings.php /usr/share/nginx/www/drupal/sites/default/files` 指令
![](img/Pasted%20image%2020210111133013.png)
更新 SELinux 的設定；在終端機下 `semanage fcontext -a -t httpd_sys_rw_content_t '/usr/share/nginx/www/drupal(/.*)?' && restorecon -Rvv /usr/share/nginx/www/drupal`
![](img/Pasted%20image%2020210111131057.png)
打開 Drupal 網站，開始初始化設定；開瀏覽器連到 `http://{你的 IP }` 網址
![](img/Pasted%20image%2020210111130439.png) ![](img/Pasted%20image%2020210111130459.png) ![](img/Pasted%20image%2020210111133856.png) ![](img/Pasted%20image%2020210111134508.png) ![](img/Pasted%20image%2020210111134554.png) ![](img/Pasted%20image%2020210111134620.png)

# 評量題目
## 查看硬碟配置
在終端機下 `df` 指令
## 查看密碼檔
在終端機下 `ll /etc/passwd` 指令
## 測試對外連線
在終端機下 `ping 8.8.8.8` 指令
## 查看 MariaDB 服務
在終端機下 `systemctl status mariadb` 指令
## 查看 Drupal 網站
開瀏覽器連到 `http://{你的 IP }` 網址
## 修改 root 密碼
在終端機下 `passwd` 指令