#虛擬化應用平台部署

# ESXi 主機加入 Active Directory 網域
## 安裝 vSphere Client
在 CLT 主機安裝 vSphere Client
![](img/Pasted%20image%2020201125134334.png)
![](img/Pasted%20image%2020201125134547.png)
![](img/Pasted%20image%2020201125134710.png)
![](img/Pasted%20image%2020201125134950.png)
![](img/Pasted%20image%2020201125135059.png)
![](img/Pasted%20image%2020201125135207.png)
![](img/Pasted%20image%2020201125135332.png)
![](img/Pasted%20image%2020201125135405.png)
![](img/Pasted%20image%2020201125135459.png)
![](img/Pasted%20image%2020201125135531.png)
![](img/Pasted%20image%2020201125135705.png)
vSphere 在 6.5 版之後已改為 web 版，不再使用 Desktop 版
![](img/Pasted%20image%2020201125141752.png)

## 將 ESXi 主機加入 AD
![](img/Pasted%20image%2020201125141828.png)
![](img/Pasted%20image%2020201125142105.png)
![](img/Pasted%20image%2020201125142150.png)
可以雙開
![](img/Pasted%20image%2020201125142344.png)


![](img/Pasted%20image%2020201125152708.png)
![](img/Pasted%20image%2020201125153141.png)
![](img/Pasted%20image%2020201125153232.png)
![](img/Pasted%20image%2020201125153434.png)
![](img/Pasted%20image%2020201125153512.png)
![](img/Pasted%20image%2020201125154233.png)

## 測試
檢查有沒有順利加入 AD 網域
![](img/Pasted%20image%2020201125154622.png)
明明已經加入 AD 網域了，為什麼不能用 AD 的管理者登入呢？
![](img/Pasted%20image%2020201125154838.png)
ESXi 系統需要把 AD 的帳號加入
![](img/Pasted%20image%2020201125155106.png)
![](img/Pasted%20image%2020201125155227.png)
![](img/Pasted%20image%2020201125155253.png)

>如果看不到 VI 網域
![](img/Pasted%20image%2020201125160403.png)
把 ESXi 主機重新開機
![](img/Pasted%20image%2020201125160238.png)

![](img/Pasted%20image%2020201125160518.png)
![](img/Pasted%20image%2020201125160723.png)
![](img/Pasted%20image%2020201125160927.png)
再登入 AD 網域看看
![](img/Pasted%20image%2020201125163038.png)
順利登入囉！
![](img/Pasted%20image%2020201125163329.png)
