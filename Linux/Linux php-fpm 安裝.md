#Linux 

php-fpm 是 php 的服務
\+ ACL 的符號，之後再解釋

# 安裝 php-fpm
## 安裝套件
`yum install php php-fpm -y`
> 只安裝 php-* 套件（除了 php-pear-\*）
`yum install $(yum search php-* | awk '{print $1}' | grep -v php-pear | tail -n 32) -y`

## 設定檔
這裡會用到的設定檔總共有 2 個

### /etc/php-fpm.d/www.conf
`vim /etc/php-fpm.d/www.conf`
這裡修改的重點是，因為我們用 Nngix，所以要讓 Nngix 可以存取 php-fpm 的設定檔
- 將 user 及 group 的設定值，從 apache 改為 nginx
- 將 listen 的設定值，從 /run/php-pfm/www.sock 改為  /run/php-pfm/php72.sock - 監聽 php72 版的 socket
> 如果沒有 /run/php-pfm/php72.sock 這個檔案的話，就用原來的 /run/php-pfm/www.sock
- 在 listen.allowed_clients=127.0.0.1 前面加上 ';' ，將這行註解，表示不監聽 127.0.0.1
- 記下 ==php_value\[session.save_path]== 的路徑 **/var/lib/php/session**，因為要讓 nginx 可以存取 session，所以需要修改 session 的權限

![](img/Pasted%20image%2020201207101953.png) ![](img/Pasted%20image%2020201207102534.png)
中間的部分是效能的調校，先不談
![](img/Pasted%20image%2020201207102939.png)

### /etc/nginx/conf.d/php-fpm.conf
將 `unix:/run/php-fpm/www.sock` 改為 `unix:/run/php-fpm/php72.sock`
> 如果沒有 /run/php-pfm/php72.sock 這個檔案的話，就用原來的 /run/php-pfm/www.sock
![](img/Pasted%20image%2020201214130211.png)

## 修改檔案權限
這裡需要修改權限的檔案總共只有 1 個

 ### /var/lib/php/session 
擁有群組從 apache 改為 nginx
`chgrp nginx /var/lib/php/session`
> 或直接將 /var/lib/php 下所有的檔案擁有者改為 nginx
`chown -R nginx /var/lib/php`

## 啟動 php-fpm 服務 
`systemctl enable --now php-fpm`
或
```
systemctl enable php-fpm
systemctl start php-fpm
```

# 測試
## 重新載入設定
```
nginx -t
nginx -s reload
restorecon -Rvv /usr/share/nginx/html/
```

## 瀏覽器開網頁
### 在 /usr/share/nginx/html/ 內放入網站資料
`echo '<?php phpinfo();?>' > /usr/share/nginx/html/test.php`

在虛擬機用瀏覽器連線到 localhost
在虛擬機用瀏覽器連線到 虛擬機_IP
如果順利看到 php 的相關資訊就沒問題了


# 故障排除
檢查 `/var/log/messages` 和 `/var/log/nginx/error.log` 內的錯誤訊息

# 移除 php, php-fpm
```
yum remove $(rpm -qa | grep php)
rm -rf /etc/php-fpm.d/
```

---
舊版
# 修改 [[Linux/Nginx 設定]]
這裡會用到的設定檔總共有 4 個

## /etc/nginx/nginx.conf
1. 將第 38 - 57 行 server 區塊轉貼到 /etc/nginx/default.d/default.conf
`cat /etc/nginx/nginx.conf | head -n 57 | tail -n 20 >/etc/nginx/default.d/default.conf`
2. 將第 38 - 57 行 server 區塊（用 { } 包起來的部分） 用 ';' 註解掉，第 45 行的 `include /etc/nginx/default.d/\*.conf;` 不註解

## /etc/nginx/default.d/php.conf
1. 將設定值轉貼到 ==/etc/nginx/default.d/default.conf==
`cat /etc/nginx/default.d/php.conf >> /etc/nginx/default.d/default.conf`
2. 移開設定檔
`mv /etc/nginx/default.d/php.conf /root`

## /etc/nginx/conf.d/php-fpm.conf
1. 將設定值轉貼到 ==/etc/nginx/default.d/default.conf==
`cat /etc/nginx/conf.d/php-fpm.conf >> /etc/nginx/default.d/default.conf`
2. 移開設定檔
`mv /etc/nginx/conf.d/php-fpm.conf /root`

## /etc/nginx/default.d/default.conf
將 ==/etc/nginx/default.d/default.conf== 的 `include /etc/nginx/default.d/\*.conf;` 刪除
調整 default.conf 設定檔順序，變成下圖
![](img/Pasted%20image%2020201204232959.png)