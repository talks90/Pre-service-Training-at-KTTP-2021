#CCNA

# IP 是什麼？
- IP 是 TCP/IP 協定套件組裡，在網路層實作的服務
- IP 被設計為低成本的協定；只提供來源主機透過網路向目的主機傳送封包必要的功能而已，至於封包流向的追蹤和管理它都不管，如果有需要，就由其他層別的協定來實作

# IP 的基本特徵
## 非連接導向 - 發封包前不跟目的建立連線
![](../img/Pasted%20image%2020201027200714.png)
- 非連接導向就好像我們寄信之後，不知道收信人會不會收到信、何時收到信一樣
- IP 也不需要協定資料單元 (PDU) 標頭中包含其他欄位來維持建立的連線，因此顯著降低了 IP 的成本

## 盡力（不可靠）- 不保證封包一定送到
![](../img/Pasted%20image%2020201027200814.png)
- 雖然 IP 是盡力（不可靠）傳送協定，但這不是說 IP 有時工作正常，有時工作不正常，也不代表它是比較差的資料通訊協定；這裡的不可靠只是表示 IP 沒有管理跟復原未送達封包或已損壞封包的能力
- 根據 IP 位置傳輸的封包，裡面不包含可以通知發送者封包是否傳送成功的相關訊息、封包標頭裡也沒有用來追蹤封包傳輸順序的同步資料、更加沒有傳輸封包的確認資訊，所以，IP 協定根本就沒有錯誤控制的機制來追蹤封包是不是好的。因此目的主機收到封包時，就算封包有損壞、順序錯亂、或者根本就沒有傳送成功，封包也不能重傳
- 遇到封包順序錯亂或遺失導致應用程式使用資料時出現問題，則上層服務（如 TCP）要解決這些問題
- 在 TCP/IP 協定套件中，由傳輸層來處理可靠性的問題；依對可靠性的需求程度，來決定用 TCP 還是 UDP
- 這樣的處理機制可以讓 IP 非常有效率地運作、適應能力更強、更能適應不同的通訊類型

## 媒體獨立性 - 封包傳送的運作跟傳輸資料是透過什麼媒介沒有關係
![](../img/Pasted%20image%2020201027200911.png)
- 網路層可以透過各種不同的媒介來傳送 IP 封包，不限於任何特定的媒體
- 對於傳輸的媒體，網路層只在意一個重要因素：各種媒體可以傳輸的最大 PDU 大小；這個特徵稱為最大傳輸單位 (MTU)
-  MTU 值由資料鏈結層傳給網路層，網路層由此來確定可以傳送封包的大小
-  有時，中介裝置（通常是路由器）把封包從一個媒體轉送到更小 MTU 的媒體時，必須拆分封包；這個程序叫做封包分片或分片

# IPv4 封包分 2 部分
## 標頭
- 用來確定封包特徵

標頭內較重要的欄位說明如下：![](../img/Pasted%20image%2020201027201946.png)
### 版本
- 4 位元，用來宣告這個封包所使用的 IP 版本；IPv4 封包這裡的值是 0100，IPv6 封包則是 0110

### 區分服務 (DS)
- 8 位元，用來確定封包的優先權；以前叫做服務類型 (ToS) 欄位
- 前 6 位元用來確定服務品質 (QoS) 機制使用的區分服務代碼點 (DSCP) 值
- 後 2 位元用來確定[顯式擁塞通知 (ECN)](https://zh.wikipedia.org/wiki/%E6%98%BE%E5%BC%8F%E6%8B%A5%E5%A1%9E%E9%80%9A%E7%9F%A5)值，可以防止網路擁塞時遺失封包

### 存活時間 (TTL)
- 8 位元，用來限制封包的壽命；單位是秒，但通常稱為跳數。
- 封包發送者會設定存活時間 (TTL) 的初始值，封包每經過一次路由器或跳處理，就先把這個數值 - 1 。當最後一個路由器把 TTL - 1 = 0 時，除了把該封包丟掉之外，還會發送 Internet 控制訊息協定 (ICMP) 逾時訊息給來源 IP 位址
- `traceroute` 命令就是利用此欄位來確定來源和目的間所使用的路由器

### 協定
- 8 位元，用來表示封包裡裝的資料承載類型，方便網路層把資料傳給相對應的上層協定
- 常用的值包括 ICMP (1)、TCP (6) 和 UDP (17, 0x11)

### 來源、目的 IP 位址
- 各 32 位元，表示封包來源及目的 IP 位址
- 在封包從來源到目的的期間，這 2 個位址通常不會改變

剩餘的欄位是用來確定及驗證封包，或者重新排列分段封包，說明如下：![](../img/Pasted%20image%2020201027205703.png)
識別和驗證封包的欄位包括：

### Internet 標頭長度 (IHL)
- 4 位元， IHL 值用來表示標頭檔的長度，最小值為 5（即 5×4 = 20 位元組 = 160 位元，也就是選項和填充欄位都是空的)；最大值為 15（即 15×4 =  60 位元組 = 480 位元）

### 總長度
- 用來表示封包的總長度，包括**標頭**和**承載（資料）**
- 單位為位元組；封包最小長度為 20 位元組（20 位元組標頭 + 0 位元組資料），而最大長度為 65,535 位元組

### 標頭總和檢查碼
- 16 位元，用來做封包的錯誤檢查；如果經過計算跟總和檢查碼的值不同，就會把封包丟掉

路由器遇到要把封包送給 MTU 比較小的另一種媒體時，需要把封包分段；這時候 IPv4 就會用封包以下的欄位來追蹤這些分段：

### 識別
- 16 位元，同一個封包的分片使用同一個識別

### 旗標
- 3 位元，標識封包的分段方式；和分片偏移量及標識欄一起幫助分片段資料重組回來

### 分片偏移量
- 13 位元，用來標識在原始封包分片前的序號

### 選項和填充欄位
- 很少用

## 承載
包含第 4 層（傳輸層）的資料段資訊，以及實際要傳送的資料


# IPv6
為什麼要發展 IPv6 呢？因為 IPv4 卡關了

- IP 位址耗盡
> IPv4 的最後 1 組 IP 已經發出去了，可是在現在這個 IoT 的世代，對 IP 位址的需求還看不到盡頭呀！

- Internet 路由表膨脹
> 雖說路由器會用路由表來確定最佳路徑，但隨著 Internet 上的路由數量不斷增加，這些 IPv4 路由器會消耗掉 Internet 上路由器的大量記憶體和處理器資源
- 缺乏端對端連線
> - RFC 1918 規範了私有位址與**網路位址轉換** (NAT)，對於減緩 IPv4 位址空間的耗盡起了不可或缺的作用
> - NAT 讓多種裝置可以分享單一的公開 IP 位址
> - 但為了要分享公開 IP ，內部網路的主機 IP 就需要被隱藏，這時如果又有單點連線的需求就有技術上的難題

## IPv6 封包
IPv6 的標頭大大簡化了 IPv4 的標頭![](../img/Pasted%20image%2020201027221252.png)

- IPv6 標頭包含 40 個八位元組（主要為來源和目的 IPv6 位址的長度），區分為 8 個標頭欄位（3 個 IPv4 基本標頭欄位和 5 個附加標頭欄位）；其中流標籤是 IPv4 所沒有的新欄位

## IPv6 把標頭簡化了有什麼好處呢？
- 路由效率更高，效能和轉送速伸縮性更佳
- 不用處理總和檢查碼
- 相對 IPv4 的「選項」欄位而言，IPv6 有更簡單和有效的延伸標頭機制
- 每個流處理均帶有「流標籤」欄位，因此不用打開封包就可以確定傳輸層的各種通訊流量

## IPv6 標頭

![](../img/Pasted%20image%2020201027222145.png)


### 版本
> 4 位元，用來表示 IP 封包的版本； IPv6 的封包，這個值唯一支持 0110

### 流量類別
> 8 位元，相當於「IPv4 區分服務 (DS)」欄位；包含用來分類封包的 6 位元區分服務代碼點 (DSCP) 值和用來流量擁塞控制的 2 位元顯式擁塞通知 (ECN)

### 流標籤
>  20 位元，用來為即時應用程式提供特殊服務。可以用來通知路由器和交換器為封包流維護同一條路徑，確保不會重新排序封包

### 承載長度
> 16 位元，相當於 IPv4 標頭的總長度欄位；用來定義整個封包（分段）的大小，包括標頭和選擇性延伸

### 下一標頭
> 8 位元，功能相當於「IPv4 協定」欄位。表示封包傳送的資料承載類型，讓網路層將資料傳送到對應的上層協定；如果有選擇性的延伸標頭加到 IPv6 封包裡時，也會使用此欄位

### 跳數限制
>  8 位元，取代了 IPv4 的 TTL 欄位。封包傳送期間，經過的每個路由器都會使此值 -1。當跳數達到 0 時，會丟棄此封包，且向發送主機轉送 ICMPv6 訊息來指明封包未到達目的

### 來源及目的位址
> 各 128 位元，用來標示發送主機及接收主機的 IPv6 位址

IPv6 封包還可以再加延伸標頭 (EH)，來提供選擇性的網路層資訊。延伸標頭是選擇性項目，位於 IPv6 標頭及承載之間。EH 用於分段、安全性、行動性支援等。
