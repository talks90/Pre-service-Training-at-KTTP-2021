#虛擬化應用平台部署

# 安裝 Active Directory 網域服務
> 安裝 AD 的機器，很多設定不能變動
所以主機名稱跟 IP 都要設定好喔！

在終端機下 `dcpromo` 指令，安裝 Active Directory 網域服務
![](img/Pasted%20image%2020201123090542.png)
![](img/Pasted%20image%2020201123090554.png)
![](img/Pasted%20image%2020201123090637.png)
![](img/Pasted%20image%2020201123090744.png)
網域名稱依需要設定，這裡用 ==vi.com== 做示範
![](img/Pasted%20image%2020201123090837.png)
![](img/Pasted%20image%2020201123090859.png)
![](img/Pasted%20image%2020201123090911.png)
![](img/Pasted%20image%2020201123091013.png)
![](img/Pasted%20image%2020201123091118.png)
![](img/Pasted%20image%2020201123091139.png)
還原模式的密碼，密碼要符合複雜度，需要還原時使用；一般來說不會用到
![](img/Pasted%20image%2020201123091233.png)
![](img/Pasted%20image%2020201123091519.png)
![](img/Pasted%20image%2020201123091853.png)
重新啟動電腦之後，先不要登入，我們來觀察一下帳號的變化
![](img/Pasted%20image%2020201123091943.png)
![](img/Pasted%20image%2020201123095232.png)


## 設定 DNS
### 開啟反向對應區域的設定
![](img/Pasted%20image%2020201123095450.png)
![](img/Pasted%20image%2020201123095843.png)
![](img/Pasted%20image%2020201123095855.png)
![](img/Pasted%20image%2020201123095953.png)
![](img/Pasted%20image%2020201123100053.png)
![](img/Pasted%20image%2020201123100103.png)
![](img/Pasted%20image%2020201123100157.png)
![](img/Pasted%20image%2020201123100305.png)
![](img/Pasted%20image%2020201123100315.png)

### 將網域內電腦加入 DNS 清單
#### IP 表
主機 | 位址（ xx 表座號）
--- | ---
adnstor | 192.168.1xx.1
esxi01 | 192.168.1xx.101
esxi02 | 192.168.1xx.102
vc | 192.168.1xx.250

在網域的正向對應區域內容，滑鼠按右鍵開始**新增主機**
![](img/Pasted%20image%2020210113094101.png)
![](img/Pasted%20image%2020201123100427.png)
![](img/Pasted%20image%2020201123100438.png)
![](img/Pasted%20image%2020201123101122.png)

### 測試 DNS 設定
![](img/Pasted%20image%2020201123105255.png)
![](img/Pasted%20image%2020201123105131.png)
手動把 adnstor 加入反向對應區清單
![](img/Pasted%20image%2020201123105021.png)

## 新增 AD 使用者
![](img/Pasted%20image%2020201123110811.png)
![](img/Pasted%20image%2020201123111004.png)
![](img/Pasted%20image%2020201123111025.png)
![](img/Pasted%20image%2020201123111047.png)


![](img/Pasted%20image%2020201123111223.png)
![](img/Pasted%20image%2020201123111304.png)
![](img/Pasted%20image%2020201123111329.png)

## 電腦加入 AD 網域
電腦加入 AD 之後，就會受到 AD 管控
不是想加入就可以加入，需要網域管理者帳密才行
![](img/Pasted%20image%2020201123112618.png)
![](img/Pasted%20image%2020201123112643.png)
![](img/Pasted%20image%2020201123112726.png)
![](img/Pasted%20image%2020201123112836.png)
![](img/Pasted%20image%2020201123112914.png)
![](img/Pasted%20image%2020201123113000.png)
![](img/Pasted%20image%2020201123113927.png)
![](img/Pasted%20image%2020201123113945.png)
可以用 AD 的管理者帳號登入囉
![](img/Pasted%20image%2020201123114746.png)
![](img/Pasted%20image%2020201123114932.png)
加入 AD 之後，密碼安全性原則就不能由本機設定了；需要從 AD 伺服器才能修改囉~
![](img/Pasted%20image%2020201123115431.png)
![](img/Pasted%20image%2020201123115449.png)
![](img/Pasted%20image%2020201123115523.png)


## 電腦退出網域
![](img/Pasted%20image%2020201123115826.png)
![](img/Pasted%20image%2020201123112643.png)
![](img/Pasted%20image%2020201123120057.png)
![](img/Pasted%20image%2020201123120144.png)
一樣需要網域管理者的帳密
![](img/Pasted%20image%2020201123120311.png)
![](img/Pasted%20image%2020201123120356.png)
![](img/Pasted%20image%2020201123120413.png)
![](img/Pasted%20image%2020201123120653.png)
