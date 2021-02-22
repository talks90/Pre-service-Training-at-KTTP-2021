在 Linux 裡，用 bind 這個套件來架設 DNS 伺服器

# 安裝 DNS 伺服器
`yum install -y bind`

## 設定檔
這裡會用到的設定檔總共只有 1 個

### /etc/named.conf
`vim /etc/named.conf`
DNS 主機的設定檔，設定 DNS 的 IP 位址、監聽的通訊埠
![](img/Pasted%20image%2020201214085249.png)

## 啟動服務
`systemctl enable --now named`
`systemctl status named -l` - `-l` 是列出詳細內容

## 測試
![](img/Pasted%20image%2020201207155015.png)

### 把查詢封包丟給分署的 DNS
在`/etc/named.conf` 設定檔內加入 `forwarders { 10.10.2.8; };`
![](img/Pasted%20image%2020201207155407.png)
重新啟動
`systemctl restart named`
有回應囉！！
![](img/Pasted%20image%2020201207155503.png)

---
# 新增 DNS 查詢資料
## 設定檔
這裡會用到的設定檔總共有 4 個
### /etc/named.conf
`vim /etc/named.conf`
在檔案最後面加上 `include "/etc/named/myzones";` ，來引入 myzones 的設定
![](img/Pasted%20image%2020201207155837.png)

### /etc/named/myzones
`vim /etc/named/myzones`
用來設定 DNS 主機管理了什麼網域名稱，還有相對應的設定檔是哪一個
![](img/Pasted%20image%2020201207160113.png)

### /var/named/student.zone
`vim /var/named/student.zone`
這個檔案是用來控制網域名稱下的各服務主機連結 IP 位址
![](img/Pasted%20image%2020201214090541.png)

### /etc/resolv.conf
`vim /etc/resolv.conf`
這個檔案是用來告訴機器，當有名稱解析需求時，該要怎麼處理
我們在 nameserver 部分的第 1 行加上 `nameserve [DNS_IP]` ，來把剛架好的 DNS 加入設定檔
![](img/Pasted%20image%2020201208085318.png)

#### 其他參數說明
==nameserver==
指定收到 DNS 查詢請求時，進行名稱解析的 nameserver IP位址；可以指定多部 DNS 伺服器，會依序提出查詢要求
==domain==
指定本地的網域名稱，如果查詢時的名稱沒有包含小數點，則會自動補上此處的網域名稱當做字尾，再送給DNS伺服器
==search==
這個參數不是必要的，它的功能是：在做查詢時可以嘗試搜尋看看的網域名稱；也可以有多個組合，如果有多個時用 ' ' （空白）隔開

假如我們設定
```
domain twnic.example.tw
search twnic.com.tw twnic.net.tw
```
當 DNS 伺服器在做名稱解析過程中，找不到輸入的名稱（如 pc1）相對應的IP時，會優先用 twnic.example.tw （本地的網域名稱，即 pc1.twnic.example.tw）來做查詢；如果失敗了，就會用 search 的組合 twnic.com.tw（即 pc1.twnic.com.tw）來進行解析，再失敗才換 twnic.net.tw 

## 重新繫結及重新啟動
```
restorecon -Rvv /etc/named
restorecon -Rvv /var/named
systemctl restart named
```

## 測試
`dig www.student15.example.tw`
![](img/Pasted%20image%2020201207163040.png)

# 故障排除
## 如何解決CentOS重啟後resolv.conf被重置問題

[原文網址](https://kknews.cc/news/pn6mybe.html)

# DNS 搜尋過程
![](img/76087.jpg)
1. DNS Server 遇到一個不認識的網址（如 `www.pchome.com.tw`），先問 (root)
2. 若 (root) 不認識，會回應：不知道，但可以找 .tw 查
3. 若 .tw 不認識，會回應：不知道，但可以找 .com.tw 查
4. 若 .com.tw 不認識，會回應：不知道，但可以找 .pchome.com.tw 查
5. 最後，.pchome.com.tw （就是我們用 .zone 檔管理的部分）就會告訴你 `www.pchome.com.tw` 的 IP 
6. 另外，DNS Server （就是我們用 .conf 檔管理的部分）會把查到的 IP 存到它的 cache 內，可以重複提供給之後的使用者查詢

# 安全
DNS 劫持 - 透過各種手段，讓 DNS Server 的 cache 改存成另一個 IP，達到欺騙的目的
如何防制 - 在各級 DNS Server 安裝 DNSSEC，當要申請域名時，同時說明自己是哪個 IP，並產生一把鑰匙（誰產生都可以），鑰匙會逐級上傳讓大家都擁有，來確認域名對應到的是正確的 IP
