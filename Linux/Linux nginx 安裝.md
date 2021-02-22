#Linux 

# 安裝 Nginx
`yum install nginx`

## 打開防火牆
`firewall-cmd --add-service=http`

### 防火牆其他操作
- 打開特定通訊埠
特定服務
`firewall-cmd --add-service=https`
指定通訊埠
`firewall-cmd --zone=public --add-port=8080/tcp --permanent`
> `--permanent` 指定為永久設定，否則在 firewalld 重啟或是重新讀取設定，就會失效
- 將現在的防火牆設定永久保存
`firewall-cmd --runtime-to-permanent`
- 重新載入 firewall 設定
`firewall-cmd --reload`
- 確認防火牆是否開啟
`firewall-cmd --zone=public --list-all`

## 啟動 nginx 服務
`systemctl enable --now nginx`
> 也可以分開來操作
開啟開機時啟動 nginx 服務的設定
`systemctl enable nginx`
現在啟動 nginx 服務
`systemctl start nginx`

## 測試
開啟瀏覽器看 localhost 可以看到 nginx 的畫面就是正常了

---
# 同場加映

## Nginx 是什麼？
![](img/1TrNJZqECEj0eVuJDeNKtNQ.png)
Nginx (讀作 engine x)，是一個免費的開源軟體，一個非同步框架的 web server，不過它的功用遠不僅止於 web server，它更多的用途是作為反向代理、Http Cache、負載平衡器

### 它的優勢到底是什麼？
輕量型 web sever 軟體

- 面向效能
Nginx 在處理 IO 並發與靜態文檔方面效能是比 Apache 還要好的，甚至是遠遠超越；因此在需要應對大流量的狀況下，許多公司會選擇使用 Nginx 作為 server 而不是 Apache
- 設置簡易
相比於 Apache， Nginx 因為模組化的設計，配置上是較為簡單的，不過就提供的功能與相關套件來看，則是歷史悠久的 Apache 略勝一籌
- 記憶體消耗低
一樣拿 Apache 來比較，Nginx 佔用了相對低的資源與內存，另外值得注意的是 Nginx 在高併發狀況下可以保持低資源低消耗，卻仍能保有高效能
- 反向代理＆負載平衡
Apache 當然也是做得到的，差別就體現在上面的第一點：==效能==
同樣實作這兩個功能，Nginx 可以消耗更少的資源，這也代表著在相同資源的狀況下，Nginx 可以處理更多的連線請求

## Nginx 的設定檔說明
![](img/76086.jpg)
Nginx 的主要設定檔通常會放置在 ==/etc/nginx/nginx.conf==
不同網域的設定檔則放在 ==/etc/nginx/conf.d/*.conf ==
<font color=gray>例如： /etc/nginx/conf.d/kylemocode.com.conf</font>
然後在主設定檔中的 http context 加入一行  `include /etc/nginx/conf.d/*.conf;` 即可將不同域名的設定引入，達成方便管理與修改不同域名設定的特性

接下來建立 `/etc/nginx/conf.d/default.conf` 設定檔，來為 Nginx Server 建立基礎的配置吧。

```
  1  upstream api {
  2      server localhost:5000;
  3      server localhost:5001;
  4  }
  5  
  6  server {
  7      listen 80;
  8      listen [::]:80;
  9      server_name SERVER_IP;
 10      root /home/ryan;
 11  
 12      proxy_set_header Host $http_host;
 13      proxy_set_header X-Real-IP $remote_addr;
 14      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 15  
 16      location / {
 17              proxy_pass http://api/;
 18      }
 19  }
```

==upstream==

在 upstream block 定義 request 要  proxy 到哪裡
上例代表要將請求 proxy 到 5000 及 5001 port；也就是說，這個 block 可以達到 load balancer 負載平衡的功能

==server==

這個 block 定義 server 的相關設定，包括：
- 要監聽的 port (http 為 80 ，https 為 443)
- 規定哪些 domain 或 ip 的 request 會被 nginx server 處理（server_name）

==location==

這個 block 非常重要，不過幸好概念還蠻易懂的，它其實就像是 routing 的概念，設定不同的 path 要對應到怎麼樣的設定

上例的 location 後面接的是 /，代表任何路徑都會被這個 block 給接收處理
（location 後面也是可以吃 regex 的喔，例如：location ~* \.(pl|cgi|perl|prl)$ {}）

==gzip==

gzip 其實就是在做壓縮（compression），減少 payload 或者檔案的大小以增進傳輸的效率
但有一種說法是壓縮是一種很耗費資源的行為，對特性為 single thread 的 Node.js 來說會是一種負擔
因此壓縮的任務其實可以交給 Nginx Web Server 去完成

```
  1  server {
  2  ...
  3  ...
  4  
  5      gzip on;
  6      gzip_types text/plain application/xml application/json;
  7      gzip_comp_level 9;
  8      gzip_min_length 1000;
  9  
 10  ...
 11  ...
 12  }
```

要在 Nginx 啟用 gzip 其實非常簡單，只要在 conf 裡加幾行設定就可以啟動最基本的 gzip 了

==https==

因為安全性的考量與資安意識的提升，現在幾乎大部分的網站都會使用 https 了，而要將 http 變為 https ，Nginx 居然也可以做到！！！
不過要開啟 https ，是要有 SSL certificate 的
這裡用 certbot 這個 package 來取得 Let’s Encrypt 的免費憑證（有效期限 90 天）

```
  1  sudo yum install certbotcertbot
  2  certonly --standalone -d DOMAIN_NAME
```

完成後 certificate 與 private key 會被建立在 /etc/letsencrypt/live/DOMAIN_NAME/ 路徑中
接下來在 Nginx Conf 中引用進來吧！

```
  1  server {
  2      listen 80;
  3      listen [::]:80;
  4      server_name DOMAIN_NAME;
  5      return 301 https://$host$request_uri;
  6  }
  7  
  8  server {
  9      listen 443 ssl http2;
 10      listen [::]:443 ssl http2;
 11      server_name DOMAIN_NAME;
 12      root /home/ryan;
 13  
 14      ...
 15      ...
 16  
 17      ssl_certificate /etc/letsencrypt/live/DOMAIN_NAME/fullchain.pem;
 18      ssl_certificate_key /etc/letsencrypt/live/DOMAIN_NAME/privkey.pem;
 19      ssl_session_timeout 1d;
 20      ssl_session_cache shared:SSL:50m;
 21      ssl_session_tickets off;
 22  
 23      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
 24      ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
 25      ssl_prefer_server_ciphers on;
 26  
 27      add_header Strict-Transport-Security max-age=15768000;
 28  
 29      resolver 8.8.8.8;
 30      
 31      ...
 32      ...
 33  }
```

這裡還有加入把 80 port (http) 的 request 重新導回 https 的 block
這也就是為什麼某些網站輸入的是 http，它還是會自動幫你導回 https 的原因

==load balancer==

```
  1  upstream api {
  2          ip_hash;
  3          server localhost:5000;
  4          server localhost:5001;
  5  }
  6  
  7  server {
  8      listen 80;
  9      listen [::]:80;
 10      server_name SERVER_IP;
 11      root /home/ryan;
 12  
 13      proxy_set_header Host $http_host;
 14      proxy_set_header X-Real-IP $remote_addr;
 15      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 16  
 17      location /socket.io/ {
 18              proxy_http_version 1.1;
 19              proxy_set_header Upgrade $http_upgrade;
 20              proxy_set_header Connection "upgrade";
 21              proxy_pass http://nodejs/socket.io/;
 22      }
 23  
 24      location / {
 25              proxy_pass http://api/;
 26      }
 27  }
```

這個例子也是在做負載平衡；但其實某個 request 要被導到哪裡去處理應該是要有不同規則的，而每個規則都有各自適合使用的時機，以下簡單介紹幾個較常見的規則：

==round-robin==

預設是採用輪詢的方式，也就是將請求輪流按照順序分配給每一個 server
假設所有伺服器的處理效能都相同，不關心每臺伺服器的當前連線數和響應速度；適用於伺服器組中的所有伺服器都有相同的軟硬體配置並且平均伺服器請求相對均衡的情況

==Weight Round Robin==

加權輪詢方式，可以設定不同 server 的權重，例如以下範例：

```
  1  upstream myweb {
  2  	server web1.dtask.idv.tw weight=3;
  3  	server web2.dtask.idv.tw weight=2;
  4  }
```

==least-connected==

最少連線；顧名思義，連線進來時會把 Request 導向連線數較少的 Server

==IP-hash==

依據 Client IP 來分配到不同台 Server；會透過雜湊函式（Hash）將一個 IP 地址對映到一臺伺服器
先根據請求的目標 IP 地址，作為雜湊鍵（Hash Key）從靜態分配的散列表找出對應的伺服器；除非斷線或 IP變動，否則同個 IP 的請求都會導入到同一個 server

---

## 移除 Nginx
移除套件
`yum remove nginx`
刪除設定檔及殘存檔案
`rm -rf /etc/nginx`
