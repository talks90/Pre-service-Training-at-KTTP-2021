#CCNA

# 簡介
是網卡的身份證，全球唯一的號碼

怎麼知道我的網卡的MAC呢？
`ipconfig –all`
 ![](../img/Pasted%20image%2020201026093236.png)

# MAC 的結構
組織唯一識別碼 (OUI；廠商編號) | 廠商分配（流水號；含網卡、介面）
--- | ---
**24 位元** | **24 位元**
6 個十六進制數字 | 6 個十六進制數字
00-60-2F | 3A-07-BC
Cisco | 特定裝置

# 表示方式
使用符號 | 範例
--- | ---
破折號 | 00-60-2F-3A-07-BC
冒號 | 00\:60\:2F\:3A:07:BC
句點 | 0060.2F3A.07BC