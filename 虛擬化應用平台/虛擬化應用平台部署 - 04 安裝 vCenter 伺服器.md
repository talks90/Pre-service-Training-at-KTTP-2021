#虛擬化應用平台部署

# 安裝 vCenter 伺服器
vCenter 是對 ESXi 的整合管理系統

## vCenter 主機加入 Active Directory 網域
設定 IP
![](img/Pasted%20image%2020201127142458.png)
關閉防火牆
![](img/Pasted%20image%2020201127142241.png)
修改電腦名稱
![](img/Pasted%20image%2020201127151432.png)
加入網域
![](img/Pasted%20image%2020201127152532.png)
> 加入前先做快照

## 安裝 vCenter SVR 相關套件
總共有 4 個套件要安裝
**簡單安裝**是全部安裝，中途問相關設定
**自訂安裝**是自己個別安裝套件
![](img/Pasted%20image%2020201130135525.png)
![](img/Pasted%20image%2020201130140634.png)


![](img/Pasted%20image%2020201130141241.png)
![](img/Pasted%20image%2020201130141301.png)
![](img/Pasted%20image%2020201130141406.png)
![](img/Pasted%20image%2020201130141503.png)
![](img/Pasted%20image%2020201130141540.png)
![](img/Pasted%20image%2020201130141612.png)
![](img/Pasted%20image%2020201130141620.png)
![](img/Pasted%20image%2020201130141635.png)
![](img/Pasted%20image%2020201130142338.png)
![](img/Pasted%20image%2020201130142522.png)
![](img/Pasted%20image%2020201130143728.png)
![](img/Pasted%20image%2020201130143921.png)
![](img/Pasted%20image%2020201130143952.png)
![](img/Pasted%20image%2020201130144034.png)
![](img/Pasted%20image%2020201130144042.png)
![](img/Pasted%20image%2020201130144104.png)
![](img/Pasted%20image%2020201130144153.png)
![](img/Pasted%20image%2020201130151024.png)
![](img/Pasted%20image%2020201130151054.png)

### 故障排除
安裝 vCenter 需要有 .NET Framework 3.5
光碟機內改成 server2012 ISO 檔
![](img/Pasted%20image%2020201130135632.png)
![](img/Pasted%20image%2020201130135737.png)
![](img/Pasted%20image%2020201130135826.png)
![](img/Pasted%20image%2020201130135859.png)
![](img/Pasted%20image%2020201130140123.png)
