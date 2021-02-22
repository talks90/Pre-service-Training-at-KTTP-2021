#Docker

# 容器網路存取原理
## 簡介

Docker 在啟動時會新增一個虛擬橋接器，預設名稱為 **docker0** （windows 10 下為 **vEthernet**），用來連接容器的虛擬網路，作為容器對外的出口![](img/Pasted%20image%2020201213145612.png)

centos8 ![](img/Pasted%20image%2020201210085004.png)![](img/Pasted%20image%2020201210085140.png)
windows10 ![](img/Pasted%20image%2020201210082927.png)

虛擬橋接器 docker0 會和主機端的對外網卡（上圖黃色區塊的 eth0）相連，以取得對外連線的能力
- 主機端的對外網卡 ( eth0 ) IP 位址，以及虛擬橋接器 docker0 連接容器的虛擬網段，會從還未使用的私有網路範圍內隨機選一個
- IP 的選擇會依照 [RFC 1918](https://tools.ietf.org/html/rfc1918) 的定義隨機選取

虛擬橋接器 docker0 同時也開啟一個介面（vethXXXXXXX），來和容器端的網卡（上圖深綠色區塊的 eth0）相連 ![](img/Pasted%20image%2020201210093809.png)

## 什麼是橋接器 Bridge
這裡用到了橋接器 (Bridge)，這個東西我們之前沒有接觸過，先來了解一下它吧！

在乙太網路的演進中，首先出現的就是**橋接器 (bridge)**
當時主要用於連接多個匯流排拓樸之傳統乙太網路，成為「一個」大型乙太網路，
劃分區段的主要目的在於切開碰撞網域，進而減少碰撞機率，提升平均頻寬

> 那為什麼不多加幾個連接埠，將碰撞領域切得更細呢？

之後，多埠橋接器 (multiport bridge) 就被稱為**交換器 (switch)**
也因此橋接器 (bridge) 與交換器 (switch) 常被視為相等的
在 IEEE 文件上，規範的術語主要使用「橋接器」，實際的裝置則多稱「交換器」

橋接器主要功能在於，決定是否讓資料訊框 (Frame) 通過它到另一端網路上
1. 當橋接器收到訊框後，會將訊框目的 MAC 與它所維護的 table 做比對
2. 如果 table 中能找到符合的位址，則進一步確認封包與目的地電腦是否為同一區段網路
3. 若封包與目的地電腦屬於同一區段網路，目的地電腦不需要透過橋接器就能收到資料（發揮 filter 功能）
4. 如果不是的話，橋接器就會把訊框傳至目的地的電腦所在區段網路（發揮forward功能）
> 橋接器雖然被用來分割網路，但是它並不會隔離廣播或多點傳播的封包喔
> 對了！橋接器是第二層的設備

# 網路模式
可以用 `docker network ls` 列出所有 network

## None
- 參數 - `-net=none`
這個參數是告訴 Docker，不要幫我管理任何網路功能，只要建立一個隔離網路空間（Network namespace）就好

建立出來的容器只有一個 loopback 的介面，沒有其他任何網路卡介面，所以這個容器沒有對外上網的能力 ![](img/Pasted%20image%2020201217105023.png)
大概會像下圖所示，在系統中產生一個容器，但是這個容器跟主機沒有任何互動，網路關係上就是**毫無關係** ![](img/Pasted%20image%2020201217105320.png)

- 使用情境
要開發網路模型、或想研究 Docker 網路時，可以用這種模式創建乾淨網路，然後開始透過各種方式讓容器可以上網

## Host
- 參數 - `-net=host`
這個參數是告訴 Docker，直接和主機共用網路，不需要建立隔離的網路空間（Network namespace）
> 共用內容包含了網卡、路由表、防火牆 … 等

本機 ![](img/Pasted%20image%2020201217111617.png) ![](img/Pasted%20image%2020201217112019.png)
容器 ![](img/Pasted%20image%2020201217112116.png) ![](img/Pasted%20image%2020201217112143.png)
是不是設定都是相同的！！

大概會像下圖所示，這種模式下的容器可以直接當成是在 HOST 上執行的一個服務，如果容器上的程式有使用 port ，主機上的那一個 port 就被占用；不需要下 `-p / --publish` 參數 ![](img/Pasted%20image%2020201217112703.png)

> 目前這個模式只能在 Linux 上使用，無法在 Windows 上使用
本機 ![](img/Pasted%20image%2020201217130227.png)
容器 ![](img/Pasted%20image%2020201217130319.png) ![](img/Pasted%20image%2020201217130737.png)

- 使用情境
	- 想要直接存取容器，不希望封包內容被其他路徑處理（網路效能與存取本機相同）
	- 容器需要使用特殊硬體資源，但如果要掛載到容器卻相對麻煩時
	- 需要容器對主機的網路環境進行操作或監控時
> 特別注意 Port Number 的使用，如果啟用到相同服務時就很容易發生衝突

## Bridge
- 參數 - `-net=bridge`
這個參數是告訴 Docker，要透過虛擬橋接器來和主機的網路互動，並且會建立一個全新的網路空間 network namespace
> 這裡先忽略 iptables 的任何規則

Bridge 是 **Docker 的預設網路模式**，它會執行下列步驟：
1. 在容器內創建一張網卡，並且指派相關的 IP addresses
2. 在主機創建一個 Linux Bridge
3. 透過 veth 把主機跟容器這兩個不同的網路空間連接在一起
> veth 概念較複雜，在此暫不探討，只要先知道是一個特殊的方式來串連不同網路空間即可

可以看到容器的 eth0 網卡後面了接了 if22，表示對映到主機的第 22 號 veth 介面 ![](img/Pasted%20image%2020201217132751.png)
主機第 22 號 veth 介面一樣也對映到容器的 eth0 網卡 ![](img/Pasted%20image%2020201217132927.png)

大概會像下圖所示，建立容器之外，還會建立一個橋接器，再由橋接器分別連到容器及主機 ![](img/Pasted%20image%2020201217195901.png)
當新增其他容器時，因為當前系統上已經有 **docker0** 可以使用，所以 Docker 就不用再創建新的橋接器，而是直接在橋接器上附加 **veth** 介面，這些介面都各自連接到各自的容器 ![](img/Pasted%20image%2020201217201248.png)

- 使用情境
	- 需要容器能夠對外上網；對網路沒有要求，單節點彼此能通就好
	- 各容器之間可以透過 IP 的方式互通
> 其實能夠對外上網還需要 iptables 幫忙，但這裡先跳過 iptables 的部分


## container
參數 - `-net=container:Name/ID`
這個參數是告訴 Docker，直接使用指定容器的網路空間，和它共處於相同的網路環境中；因此，這兩個 Container 將會看到一樣的網路介面、路由表 … 等網路相關資訊（除了網路之外，其他的部分還是隔離獨立的）
![](img/Pasted%20image%2020201220225242.png) ![](img/Pasted%20image%2020201220225308.png) ![](img/Pasted%20image%2020201220225344.png)

大概會像下圖所示，新建的容器會使用舊有容器的網路空間 ![](img/Pasted%20image%2020201220230905.png)

- 使用情境
	-   共享相同網路空間內的容器因為共享 **lo**，所以可以直接用 localhost 來存取彼此服務
	-   Kubernetes 的 Pods 是利用這個網路模式做出來的，所以 Kubernetes Pod 裡面可以有多個容器，並且彼此可以使用 localhost 來互相存取彼此的服務

---
# 參考資料
- [2019 iT 邦幫忙鐵人賽 - 用js成為老闆心中的全端工程師系列 第 30 篇 ](https://ithelp.ithome.com.tw/articles/10206725)
- [NotFalse 技術客 - 中繼器 (Repeater)、集線器 (Hub)、橋接器 (Bridge)、交換器 (Switch) 原理與介紹](https://notfalse.net/66/repeater-hub-bridge-switch)
- [小信豬的原始部落 - Docker 網路簡介](https://godleon.github.io/blog/Docker/docker-network-overview/)
- [Hwchiu Learning Note - Docker Network - 網路模型](https://www.hwchiu.com/docker-network-model.html)