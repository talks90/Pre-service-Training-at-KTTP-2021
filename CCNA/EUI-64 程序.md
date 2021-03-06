#CCNA 

# 什麼是 EUI-64 程序
- IEEE 定義了延伸唯一識別碼 (EUI) 或修改 EUI-64 程序。該程序使用用戶端的 48 位元的乙太網路 MAC 位址，並在該 48 位元 MAC 位址的中間插入另外 16 位元來新增 64 位元介面 ID
- EUI-64 優勢在於可以使用乙太網路 MAC 位址確定介面 ID。這允許網路管理者使用唯一 MAC 位址輕鬆追蹤終端裝置的 IPv6 位址。但是，這卻為很多客戶帶來了隱私憂慮。他們擔心他們的封包可以追溯到實際實體電腦。因為這些顧慮，可以轉而使用隨機產生的介面 ID

![](../img/Pasted%20image%2020201104005356.png) EUI-64 介面 ID 以二進制表示，共分 3 個部分
1. 用戶端 MAC 位址有 24 位元 OUI，但是第 7 位元（通用/本地 (U/L) 位元）顛倒。這意味著，如果第 7 位元是 0，則它會變為 1，反之亦然
> 結果即是產生的 EUI-64 介面 ID：FE99:47FF:FE75:CEE0
> 註：U/L 位元的用途和該位元值的翻轉原因會在 RFC 5342 中討論。
2. 插入十六進制值 FFFE（二進制形式為 1111 1111 1111 1110）
3. 用戶端 MAC 位址的 24 位元裝置識別碼
