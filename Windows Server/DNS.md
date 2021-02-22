#Windows伺服器環境規劃建置 #DNS

# DNS 係蝦米？！
- DNS 的全稱是 Domain Name System(或 Service)
- 當要連上一個網站時﹐原則上是要輸入 216.239.53.101 這樣的 IP 才行，但這樣的數字字串能有幾人記得住，所以我會幫它命名為 www.google.com 好方便記憶；當我們在URL打上﹕www.google.com 的時候﹐ DNS 會幫忙轉換成 216.239.53.101 

# DNS 的結構
- DNS 是一個分層級的分散式名稱對應系統﹐有點像電腦的目錄樹結構﹕在最頂端的是一個“root”﹐然後其下分為好幾個基本類別名稱﹐如﹕com﹑org﹑edu 等﹔再下面是組織名稱﹐如﹕ibm﹑microsoft﹑intel 等﹔繼而是主機名稱﹐如﹕www﹑mail﹑ftp 等
- 因為當初 internet 是從美國發展起的﹐所以當時並沒有國域名稱﹐但隨著後來 internet 的蓬勃發展﹐DNS 也加進了諸如 tw﹑hk﹑cn 等國域名稱。所以一個完整的 dns 名稱就好像是這樣的﹕www.xyz.com.tw﹐而整個名稱對應的就是一個(或多個) IP 位址了。
- 在早期的設計下﹐root 下面只有 6 個組織類別

類別名稱 | 代表意思
:---: | :---:
edu | 教育﹑學術單位
org | 組織﹑機構
net | 網路﹑通訊單位
com | 公司﹑企業
gov | 政府機關
mil | 軍事單位

# DNS 的運作
- 在設定 IP 網路環境的時候﹐都要告訴每台主機關於 DNS 伺服器的位址(我們可以手動的在每一台主機上面設置﹐也可以使用 DHCP 來指定)。但這設定的義意何在呢？其目的就是請 DNS 幫忙解析主機名稱與 IP 位址啦
- 在這個設定過程中，DNS 被稱為 resolver (也就是負責解析的 DNS Server)，而被設定主機，則只是單純的 DNS Client 了，也就是提出解析請求的主機
- 下面讓我們看看 DNS 是怎樣運作的
	1. 客戶端向伺服器提出查詢項目
	2. 當被詢問到有關本域名之內的主機名稱的時候﹐DNS 伺服器會直接做出回答
	3. 如果所查詢的主機名稱屬於其它域名的話﹐會檢查快取記憶體 (Cache)﹐看看有沒有相關資料
	4. 如果沒有發現﹐則會轉向 root 伺服器查詢
	5. 然後 root 伺服器會將該域名之下一層授權 (authoritative) 伺服器的位址告知(可能會超過 1 台)
	6. 本地伺服器然後會向其中的一台伺服器查詢﹐並將這些伺服器名單存到記憶體中﹐以備將來之需(省卻再向 root 查詢的步驟)
	7. 遠方伺服器回應查詢
	8. 若該回應並非最後一層的答案，則繼續往下一層查詢，直到獲的客戶端所查詢的結果為止
	9. 將查詢結果回應給客戶端﹐並同時將結果儲存一個備份在自己的快取記憶裡面
	10. 如果在存放時間尚未過時之前再接到相同的查詢﹐則以存放於快取記憶裡面的資料來做回應。

- 從這個過程我們可以看出﹐沒有任何一台 DNS 主機會包含所有域名的 DNS 資料﹐資料都是分散在全部的 DNS 伺服器中﹐而 NIC 只需知道各 DNS 伺服器位址就可以了
- 為了更好地理解一下 DNS 的運作﹐讓我們看看查詢 www.home.netman.com.tw 這台主機位址的過程![](../img/Pasted%20image%2020201104200455.png)
- 在這個例子中﹐www.home.netman.com.tw 這台主機的 DNS 對應資料﹐是由負責 home.netman.com.tw 這個域名的 DNS 伺服器管理的
>在 DNS 術語中﹐我們稱一個域名為“zone”﹐這個 zone 可以是您從 NIC 申請回來的域名﹐也可以是從該域名之下延伸出來的“sub-zone”
- 在這台 DNS 伺服器上面﹐必須有一個關於 home.netman.com.tw 這個 zone 的檔案﹐而這檔案裡面必須有一筆關於 www 的紀錄
> 任何主機都是以「紀錄」來表示，稱為 Resource Record
- 這個紀錄可以是一個 IP 位址﹐也可以是別名的形式，來對應一台主機名稱﹐但無論如何﹐所對應的主機名稱最終是要被一個 IP 位址所對應著就是了
- 同時﹐DNS 還能提供「反查詢」(reverse lookup) 功能﹐也就是以 IP 來查詢主機名稱。網路上面的許多服務﹐如﹕FTP, SMTP﹑等等﹐都需要到這個功能
- 建議 DNS 服務本身要用反查詢功能﹐在設定時也要為每個網路建立 reverse zone；雖然不用 reverse zone 也可以使用 DNS 服務﹐但其實有些不易察覺的問題

# DNS 的名稱記錄
- DNS 不僅僅是用來解釋位址用的﹐而且還可以回答更多關於網路和主機的其它信息﹐其中很重要的一個功能就是可以供郵件系統進行路由
- 這些資料﹐通常會以不同的「紀錄」名稱出現在DNS的資料檔案中。以下是一個 Linux 的 DNS 範例

```
;
; Zone file for siyongc.domain
;
; Then full zone file
;
$TTL 86400
@  IN	SOA	redhat52.siyongc.domain. netman.siyongc.domain. (
			1999092801	; serial
			8H		; refresh
			2H		; retry
			1W		; expire
			1D )		; minimun
;
	IN	TXT	"A test domain, created by Netman"
	IN	NS	redhat52
	IN	NS	debian.home
	IN	MX	10	redhat52.siyongc.domain.
	IN	MX	20	debian.home
;
localhost	IN	A	127.0.0.1
﹔
gw	IN	A	192.168.0.17
	IN	HINFO	"Redhat" "MASQ"
	IN	TXT	"The masquerade gateway to internet"
﹔
redhat52	IN	A	192.168.0.17
	IN	MX	10	redhat52
	IN	MX	20	debian.home
	IN	HINFO	"Dell PII 266" "Linux RedHat"
www	IN	CNAME	redhat52
mail	IN	CNAME	redhat52
ftp	IN	CNAME	redhat52
news	IN	CNAME	redhat52
smtp	IN	CNAME	redhat52
﹔
pii266	IN	A	192.168.0.15
	IN	MX	10	redhat52.siyongc.domain.
	IN	MX	20	debian.home.
﹔
slware36	IN	A	192.168.0.18
	IN	MX	10	redhat52.siyongc.domain.
	IN	MX	20	debian.home.
﹔
rhroute	IN	A	192.168.0.4
	IN	MX	10	redhat52.siyongc.domain.
	IN	MX	20	debian.home.
﹔
home	IN	ns	debian.home.siyongc.domain.
debian.home	IN	A	10.0.2.101
```

類別名稱 | 代表意思
:---: | :---
TXT | 只是一些說明文字﹐可以用來說明主機/網路環境設定
NS |	名稱伺服器﹐也就是該 zone 指定的授權 DNS 伺服器名稱
MX |	郵件伺服器﹐負責經由 DNS 查詢進行郵件傳遞的郵件伺服器。好處是﹕如果要換郵件伺服器的時候﹐只要改 DNS 紀錄就好﹐而且對方郵件伺服器不用管到底是誰負責郵件交換。還可以在 MX 後面的指定伺服器的使用順序（數字越低越優先），讓多台郵件伺服器一起分擔郵件交換工作
A |	用來對應主機名稱和其 IP 位址﹐這個紀錄最常用﹐而且也是最重要的紀錄之一
HINFO |	和 TXT 差不多﹐是回答 “Host Information” 查詢用的
CNAME |	是一個「別名」紀錄﹐可以給 A 紀錄使用另外一個（或多個）名稱讓外面查詢。CNAME 可以對應一個 A 紀錄﹐但不鼓勵對應另一個 CNAME 紀錄
AAAA |	和 A 紀錄一樣﹐只不過對應的是 IP v6 格式

# 分擔 DNS 工作
- 由於 DNS 的重要性日益顯著，為提高其容錯能力及查詢效能，我們在架設某一單一 zone 的時侯，常以多台伺服器來負責該 zone 的服務。其中，我們必需指定一台 Primary(master) DNS 伺服器，它是架設在某一個網域下被主要授權並控制所有名稱紀錄的主控伺服器﹐管轄著該網域的所有紀錄資料﹐這些紀錄資料只有 primary(master) 可以修改
- 但如果在一個比較大型的網路中﹐DNS 伺服器就會變得很繁忙﹐所以可以設定多個 DNS 來分擔 master 的工作﹐但如果每一個 DNS 伺服器去更新資料﹐容易出現錯誤或資料不同步的情形。這時可以設定其它的伺服器為 secondary (slave) DNS 來複製 master 上面的紀錄資料﹐這樣﹐其它的電腦可以被分派到不同的 DNS 做查詢﹐既可以分擔 master 的工作﹐而且資料也可以自動進行同步工作。為確保資料的一致性，master 每次更新過資料後會以 notify 機制主動通知 slave 前來同步。此外，還可以設定 DNS 資料同步的時間間隔﹐在 dns 檔案中的 Refresh 設定就是了。在檔案中，您還會看到 Serial ﹐當 slave 的上面的 serial 數字少於它﹐資料就會被複製﹐否則會被忽略

# DNS 的封包格式
![](../img/Pasted%20image%2020201104212837.png)

類別名稱 | 代表意思
:---: | :---
QID | DNS 查詢封包編號，作為確認依據
QR | 查詢封包為 0 ﹔回應為 1 。長度為 1 byte 
OPCodes | 封包類別(QUERY, IQUERY, STATUS, Reserved)。長度為 4 bytes
Flags | 共 4 bytes ，各表示：AA(Authoritative Answer)、TC(Truncation)、RD(Recursion Desired)、RA(Recursion Avalable)
Reserved | 保留未用
RCodes | 回應訊息，長 4 bytes ，除 0 及 6-15 保留未用外，1-5 分別為：Format Error、Server Failure、Name Error、Not Implemented、Refused
Question Section | 分為 NAME、TYPE、CLASS 三個子欄位，分別作為查詢、應答、授權、額外紀錄等封包之資訊，及各自長度
Answer Section | 分為 NAME、TYPE、CLASS 三個子欄位，分別作為查詢、應答、授權、額外紀錄等封包之資訊，及各自長度
Authority Section | 分為 NAME、TYPE、CLASS 三個子欄位，分別作為查詢、應答、授權、額外紀錄等封包之資訊，及各自長度
Additional Records Section | 分為 NAME、TYPE、CLASS 三個子欄位，分別作為查詢、應答、授權、額外紀錄等封包之資訊，及各自長度

# DNS 協定之 RFC 文件
- RFC-822
- RFC-883
- RFC-920
- RFC-973
- RFC-974
- RFC-1032
- RFC-1033
- RFC-1034
- RFC-1035
- RFC-1101
- RFC-1296
