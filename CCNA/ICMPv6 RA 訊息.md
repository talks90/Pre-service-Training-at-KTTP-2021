#CCNA

#  ICMPv6 RA 訊息內容 3 選 1
![](../img/Pasted%20image%2020201104004008.png)
1. SLAAC ONLY- 裝置要用 RA 訊息中的前置碼、前置碼長度和預設閘道位址資訊；[[md/#CCNA]] 伺服器不提供其他資訊
2. SLAAC & [[DCHPv6]] - 裝置要用 RA 訊息中的前置碼、前置碼長度和預設閘道位址資訊；[[md/#CCNA]] 伺服器另外提供 DNS 伺服器位址等其他資訊。這種情況下，[[md/#CCNA]] 伺服器不用分配或追蹤任何 IPv6 位址分配，僅需要提供額外資訊，所以稱為無狀態 [[md/#CCNA]]
3. [[md/#CCNA]] ONLY- 裝置不得使用 RA 的訊息；只能用 [[md/#CCNA]] 伺服器自己發現及查詢得到的位址資訊（包括 IPv6 全域單點傳送位址、前置碼長度、預設閘道和 DNS 伺服器位址）。這種情況下，[[md/#CCNA]] 伺服器就像 IPv4 的 DHCP 一樣，要分配並追蹤 IPv6 位址，也就不會幫多個裝置配到相同的 IPv6 位址，所以為有狀態 [[md/#CCNA]]