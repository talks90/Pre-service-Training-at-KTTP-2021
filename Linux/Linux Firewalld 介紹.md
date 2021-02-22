# 防火牆

防火牆的原理是把不必要的通道關閉
可分別針對**通訊埠、來源、MAC**做設定

但防火牆無法對封包內容做掃描
目前有一種做法是把封包再導給防毒軟體做內容的過濾

## Linux 防火牆
- 早期 - iptables
- 現在 - firewalld
	- 是一種系統服務，所以用 systemctl 來操作
	- 設定：文字介面是 firewall-cmd、另外也可以使用圖形介面
	- firewalld 是以區域 (zone) 的方式管理規則 (rule)，動態的方式執行；也就是說規則修改後立即生效，不需要重新啟動服務（service 或 daemon）
	- firewalld 其實還是透過 iptables 的指令，直接下給 linux kernel （內核級別的框架）去做 netfilter 的動作（netfilter 其中一個功能就是過濾封包）

> iptables 與 firewalld 兩個防火牆管理系統只能選用其中一種，不可以同時開啟，否則會出錯

### firewalld
#### firewalld 的好處是什麼？
以前的 iptables 是直接把規則餵給 kernel，這意味著，任何的變動都需要重新載入新的規則，來清除舊的規則；而這樣的動作可能會中斷已開啟的連線
而 firewalld 只會送出有變動的部分；所以，通常來說，已開啟的連線不會被影響

firewalld 有重編核心，最佳化 - 把不必要的 lib 去除，不需要再花資源去監控這些動態模組

#### 設定檔
設定檔跟執行指令是互相搭配的
設定檔的格式是 xml

- 設定檔資料夾結構
	- /usr/lib/firewalld/ - 這目錄是 firewalld 預設的設定資料，非必要不要更動；若要修改，建議作法：copy 到 /etc/firewalld 再做修改
	- /etc/firewalld/ - 存放使用者自訂的資料（以此目錄的設定為優先）
		- /etc/firewalld/firewalld.conf - 詳細說明看 `man firewalld.conf`
		- /etc/firewalld/direct.xml - 詳細說明看 `man firewalld.direct`
	- /etc/firewalld/zone/ - 詳細說明看 `man firewalld.zone`
	- /etc/firewalld/service/ - 詳細說明看 `man firewalld.service`
	- /etc/firewalld/icmptype/ - 詳細說明看 `man firewalld.icmptype`

#### 執行指令
- systemctl - 啟動 firewalld
- firewall-cmd - 規則指令，立即生效；詳細說明看 `man firewall-cmd`
- firewall-config - 設定，直接修改 /etc/firewalld/
- *其他的指令等用到再補充*

#### 規則
防火牆的規則會用到很多，所以要做分類及整理，才方便管理
存放規則的地方是 zone，最常用的 zone 是 public
zone 裡面可以允許 interface (networkCard), Source, Connection 等不同的規則

# 防火牆怎麼學？資料怎麼查？
官方文件該看什麼，自己要學會拿捏

## [[Linux/Linux firewalld zone#分類]]
## 加入 zone 方法
## rules 怎麼寫
### 對象
- service
- IP
- port
### Action
- 指令操作方法
- 設定檔編寫
- 直接研究效果最實際 [[Linux/Linux firewalld 操作範例]]

- Doos 阻斷服務攻擊，內容大部份可能都是正常訊息的封包
目前作法是：
1. 分流
2. 清洗資料 - 重覆內容丟棄

但萬一遇到流量 > 清洗站的能力時，還是會失效

## 開一個站台遇到別人來試密碼
1. disable root login
2. ban ip who try to login root

# 課堂小測驗
1. 只允許 192.168.0.0/24 網段存取
2. 只允許 ssh 使用 4444 port
3. 單一 zone 使用 NAT 方法
4. 兩個以上的 zone 使用 NAT 方法
5. 將 80 port 轉成 8080 port
6. 將 IP/ 網段鎖住 60 分鐘
7. 如何限制流量在 1 kB/s 之下 - 通常在 switch/router 上做

1. `--add-source="192.168.0.0/24"` - 老師解答
我的解法
```
firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.0.0/24" accept'
firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="0.0.0.0/0" reject'
```

2. `/etc/firewalld/services/ssh.xml 改 port="4444"` - 老師解答
我的解法
`firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" service name="ssh" port port="4444" protocol="tcp" accept'`

3. 4. 2 張網卡加入 zone，再 --add-masquerade；缺點是無法做其他靈活的運用，安全性上沒有很完整，完整的方法要用內、外部 zone
我的解法
架設 NAT 需要有兩個網路孔，一個負責 WAN，一個負責 LAN，先修改網卡的 zone 分別為   External (WAN) 跟 Internal (LAN)，假設 ens33 負責 WAN，ens34 負責 LAN
```
# nmcli c mod ens34 connection.zone internal
# nmcli c mod ens33 connection.zone external
# firewall-cmd --zone=external --add-masquerade --permanent
# firewall-cmd --zone=internal --add-masquerade --permanent
# firewall-cmd --direct --add-rule ipv4 nat POSTROUTING 0 -o ens33 -j MASQUERADE
# firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i ens34 -o ens33 -j ACCEPT
# firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i ens33 -o ens34 -m state --state RELATED,ESTABLISHED -j ACCEPT
# firewall-cmd --reload
```
4. 3. 外部 znoe --add-masquerade，內部不用開；sysctl.conf 把 ip forward 的功能打開
5. `firewall-cmd --zone=public --add-forward-port=port=80:proto=tcp:toport=8080` - 針對大流量的效果比較快；用 rich-rule 的規則是最後才載入，所以效果會比較慢
6. `firewall-cmd --zone=home --add-rich-rule="rule family="ipv4" source address="192.168.0.0/24" reject" --timeout=3600` - 單位是秒數計算
7. `iptables --append RATE-LIMIT --match limit --limit 50/sec --limit-burst 20 --jump ACCEPT`

[參考資料](https://making.pusher.com/per-ip-rate-limiting-with-iptables/)

# 偽裝
偽裝是用來隱藏自己的真實 IP，改用另一組不同的 IP 來轉送到外網
類似於 IP 分享器的功能

打開偽裝功能
![](img/Pasted%20image%2020201224140500.png)
在網卡上設定用來偽裝的 IP
![](img/Pasted%20image%2020201225102959.png)
檢查網卡是否正確設定
`ip addr show`
![](img/Pasted%20image%2020201225103445.png)

# 連接埠轉送
![](img/Pasted%20image%2020201225140123.png)
連接埠轉送是用來把防火牆外特定連接埠的請求轉到我們希望給他的連接埠
例如，我伺服器的 80 port 有一個 Drupal 站台的服務，但我不希望使用者從 80 port 可以連進來，而要改從 8000 port 進來
![](img/Pasted%20image%2020201225141225.png) ![](img/Pasted%20image%2020201225141352.png)
不只連接埠，也可以轉送給其他的 IP

# 豐富規則
豐富規則裡面可以進一步做更多細節的調整