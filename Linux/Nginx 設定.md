 nginx.org 上的 [Beginner’s Guide](http://nginx.org/en/docs/beginners_guide.html) 官方教學
 Drupal 論壇的[參考文章](https://groups.drupal.org/node/388488)
 
# 使用 Nginx
 
 ```
 # 啟動 nginx
 nginx
 
 # 加上 -s option 來下指令
 
 # 停止 nginx
 nginx -s stop
 
 # 重新讀取設定檔
 nginx -s reload 
 ```

# 自動啟動
把 nginx 啟動的 bash 檔放到 /etc/init.d/ 下方，也就是重開機後會自動執行 nginx

# 設定檔路徑
nginx 的設定檔名為 ==ngix.conf==，會依據安裝方式導致被放置的路徑不同，可以透過 `nginx -t` 來查詢

```
nginx -t
# nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
# 表示設定檔路徑為 /usr/local/etc/nginx/nginx.conf
```

# ngix.conf

要先了解 conf 檔案的架構及一些名詞。

config 檔是由一連串的 **directive** 所組成的。directive 針對特定的部分作設定，分為兩種：simple directive 及 block directive

simple directive 要以分號` ; `結尾，而 block directive 會有一組大括號` {} `，包著其他的 directive（simple 或是 block）

因此設定檔中顯眼的 ==http==、==server== 及 ==location== 都是 block directive。它們有著從屬關係

```
http {
    # server 一定在 http 裡面 
    server { 
        # location 一定在 server 裡面 
        location {}
    } 
}
```

而最底層的 block directive 只會有兩種：==http== 及 ==event==，稱之為 **main context**

simple directive 會對把它包起來的 block directive 作設定，舉最簡單的例子：
```
# listen 是個 simple directive
# 對 server 這個 block directive 作設定
# 指定它監聽 port 80
server {
listen 80;
}
```

## 設定 proxy server
如果 server app 是透過某個 port 作為介面（例如：express.js）。
這時可用 nginx 來作為 proxy server。

情境：
app 使用的 port 為 2368，要讓當連到` www.example.com `時，交由這個 app 來處理。
先修改` /etc/hosts`（需 root 權限），讓` www.example.com `指向本機
```
# /etc/hosts
127.0.0.1 www.example.com
```

再到` nginx.conf `把` www.example.com `指到 2368 這個 port
```
server {
    server_name www.example.com; 
    location / {
        proxy_pass 127.0.0.1:2368; 
    } 
}
```

## 設定 load balancer
使用 upstream 這個 directive。

假設 2 個 ip：==192.168.1.1== 及 ==192.168.1.2== 的 2368 port 都有相同的 app。

```
http { 
    upstream my_upstream {
        server 192.168.1.1:2368;
        server 192.168.1.2:2368; 
    }
    server { 
        server_name www.example.com;
        location / {
            proxy_pass http://my_upstream; # 指到設定的 upstream 及 protocol 
        } 
    }
} 
```
這樣子 nginx 會使用預設的 round-robin 原則來作 load balance

## 設定檔主檔 /etc/nginx/nginx.conf
![](img/Pasted%20image%2020201205001630.png)

## 預設主機的配置 /etc/nginx/conf.d/default.conf
![](img/Pasted%20image%2020201205001858.png)

# 什麼是 CGI、FastCGI、PHP-FPM
## Comman Gateway Interface (CGI)
在計算機中，CGI 提供了標準通訊協定讓網路伺服器執行程式腳本 (scripts)，藉此產生動態的網頁，此程式稱為 CGI Script (CGIs)，而程式如何被伺服器執行的細節由伺服器決定，通常一個請求會執行一次 CGI Script，藉此產生 HTML 回傳至 Client 端
也就是說當伺服器 (Nginx, Apache) 要執行程式腳本 (PHP, Java…) 產生網頁 (HTML) 時，就要依照 CGI 規定的通訊協定傳輸資料給程式腳本，傳遞的資訊包含 POST 資料、HTML Header、URL 等等

## FastCGI
FastCGI 是二位元的通訊協定，處理網路伺服器與程式的介面，FastCGI 是早期 CGI 的變形，其主要目的是為了減少伺服器執行 CGIs 產生過多的負載，允許伺服器同時處理多個網頁請求
CGI 應用程式在每一次的請求時產生新的程序 (process)，且在請求完成後結束此程序，此方式讓 CGI 程式容易實作，卻也在大量的請求時，持續產生及結束程序的方式會導致過度負載，此外各個程序間也無法共享資源 (資料庫連線、快取等等)
FastCGI 則是保有一個持續存在的程序，藉由此程序處理一系列的請求，而程序與伺服器間的連線會一直保持，並不會隨請求完成後而關閉。每一次請求來時，持續存在的程序產生一個進程，由此進程處理請求，因為所有的進程都是由同一個程序產生的，所以彼此間可以享有共同資源，另外伺服器也可以藉由一次傳送多個請求，或是保有多個連線的方式達到節省傳遞的成本

## PHP-FPM
PHP-FPM (PHP FastCGI Process Manager) 其實就是實踐 FastCGI 的程序，原先是第三方的外掛套件，最後被官方合併為 PHP 核心的一部分

# 如何在 Nginx 上設定 FastCGI Proxying
Nginx 本身無法處理程式腳本 (ex: PHP, Java)，所以當客戶端有這方面的請求時，Nginx 會作為代理伺服器將請求傳送給後端的伺服器，本文章將會以 php-fpm 做為後端伺服器，說明 Nginx 如何以 FastCGI 的方式與後端伺服器溝通

## fastcgi_param 與 fastcgi.conf 檔案
fastcgi_parm 與 fastcgi.conf 是由 nginx 開發者們維護出的常見設定，大部分情況下只要引入這兩個設定檔的其中一個，再加入一些設定即可，fastcgi_parm 與 fastcgi.conf 最大的差異在於 SCRIPT_FILENAME，fastcgi_parma 中有設定 SCRIPT_FILENAME，而 fastcgi.conf 則沒有，此兩個設定檔位於 Nginx 根目錄設定檔 (/usr/local/etc/nginx)

## 引入外部設定檔
可以將通用的設定寫入一個檔案，然後透過 indlude 引入這些設定，達到設定共用的效果
```
# /your/nginx/fastcgi_common
fastcgi_param REQUEST_METHOD $request_method;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```

```
root /your/project;
location ~ \.php$ {
    include fastcgi_common;
    fastcgi_param CONTENT_TYPE $content_type;
    fastcgi_param CONTENT_LENGTH $content_length;
    fastcgi_pass 127.0.0.1:9000;
}
location /scripts $ {
    include fastcgi_common;
    fastcgi_index index.php;
    fastcgi_pass unix:/usr/local/var/run/php71-fpm.sock;
}
```

## 基本設定
### 情境 1
將 http://localhost/info.php 請求傳送給後端的 php-fpm，由 php-fpm 執行 /your/project/info.php ，並將結果回傳給客戶端
```
location ~ \.php$ {
    root /your/project;
    fastcgi_param REQUEST_METHOD $request_method;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_pass 127.0.0.1:9000;
}
```
- location - 會比對符合 php 結尾的請求，並執行區塊內的設定
- root - 執行腳本的根目錄
- fastcgi_param - 設定要傳送的 fastcgi 參數，此 directive 會是是一個矩陣，使用者可以依序設定多個 fastcgi 參數，第一個值是參數名稱，第二個值是參數內容，通常會是 $ 開頭的 nginx 變數
	- REQUEST_METHOD - 客戶端以何種方式請求
		- $request_methog - Nginx 變數，記錄客戶端請求的方式
	- SCRIPT_FILE - 要執行的 script file 位置，範例中由 $document_rool, $fastcgi_script_name 兩個變數組成檔案位置
		- $document_rool - Nginx 變數，對應到 root 或 alias
		- $fastcgi_script_name - Nginx 變數，比對 URL 得到的 script file name
- fastcgi_pass - 將請求導向後端伺服器的位置，php-fpm 預設會監聽 9000 port，所以當 nginx 與 php-fpm 在同一台主機時，會將後端伺服器的位置設定為 127.0.0.1:9000

### 情境 2
將 http://localhost/scripts/ 請求傳送給後端的 php-fpm，由 php-fpm 執行 /your/project/scripts/index.php ，並將結果回傳給客戶端
```
location /scripts $ {
    root /your/project;
    fastcgi_param REQUEST_METHOD $request_method;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_index index.php;
    fastcgi_pass unix:/usr/local/var/run/php71-fpm.sock;
}
```
- fastcgi_index - 若 SCRIPT_FILENAME組成的檔案結尾是 / 時，會在尾端自動加上 fastcgi_index 形成一個完整的檔案位子
- fastcgi_pass - php-fpm除了可以透過 network socket 溝通外，也可以透過 unix socket，基於安全性考量，建議是使用 unix socket

# 如何在 Nginx 上設定 Laravel
Nginx 主要目的就是將請求傳送給 php-fpm，且 php-fpm 可以正確將請求對應到正確的 scrip 檔案，在 Laravel 中，統一的執行點會是 laravel_project/public/index，而以下的範例 Nginx 會將 http://server_domain_or_ip 導向 /your/laravel_project/public/index

```
server {
    # 監聽 80 port
    listen 80;
    listen [::]:80;
    # script 檔案的根目錄
    root /your/laravel_project/public;
    
    # 對應到的 domain 或 IP 才會進入此 server block
    server_name server_domain_or_ip;
    # 此 location directive 會將 url 轉換，目的是將 url 包含 index.php
    # ex:
    # http://server_domain/user --->
    # http://server_domain/index.php/user
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    # 上一個 location directive 轉換完後包含 index.php
	# 因此會符合此 location directive
    # 接續上一個轉換完的 url，將請求傳遞給 php-fpm
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/usr/local/var/run/php71-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
