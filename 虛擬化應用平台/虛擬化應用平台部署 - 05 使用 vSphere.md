#虛擬化應用平台部署

# 使用 vSphere
## 登入
### vSphere Web Client
![](img/Pasted%20image%2020201201140255.png)
![](img/Pasted%20image%2020201201144454.png)
![](img/Pasted%20image%2020201201144638.png)
![](img/Pasted%20image%2020201201144704.png)

#### 故障排除
需要 Flash Player
> 2021/01/01 起 Flash 已經停止服務了，所以 5.5 以下版本無法再使用囉！

![](img/Pasted%20image%2020201201140436.png)
先將網卡設成 NAT 模式
![](img/Pasted%20image%2020201201141810.png)
下載並安裝 Flash Player
![](img/Pasted%20image%2020201201142255.png)
![](img/Pasted%20image%2020201201142805.png)
![](img/Pasted%20image%2020201201142938.png)
將網卡調回原本的設定
![](img/Pasted%20image%2020201201143029.png)
![](img/Pasted%20image%2020201201143911.png)

### vSphere Client
![](img/Pasted%20image%2020201201145411.png)
![](img/Pasted%20image%2020201201145445.png)
為什麼不能登入？因為使用者帳號還沒有在 vSphere Client 中建立
![](img/Pasted%20image%2020201201145930.png)

## 建立 vCenter 使用者
![](img/Pasted%20image%2020201201150107.png)
![](img/Pasted%20image%2020201201150150.png)
![](img/Pasted%20image%2020201201150231.png)
![](img/Pasted%20image%2020201201150415.png)
### 加入 VC 的 Administrator
![](img/Pasted%20image%2020201201150502.png)
![](img/Pasted%20image%2020201201150806.png)
![](img/Pasted%20image%2020201201150924.png)
![](img/Pasted%20image%2020201201151130.png)
### 加入 AD 的 Administrator
![](img/Pasted%20image%2020201201151600.png)
![](img/Pasted%20image%2020201201151620.png)
![](img/Pasted%20image%2020201201151642.png)

### 測試
VC 的 Administrator
![](img/Pasted%20image%2020201201151754.png)
![](img/Pasted%20image%2020201201151848.png)
![](img/Pasted%20image%2020201201151933.png)
AD 的 Administrator
![](img/Pasted%20image%2020201201152101.png)
![](img/Pasted%20image%2020201201152128.png)
![](img/Pasted%20image%2020201201152154.png)
![](img/Pasted%20image%2020201201152211.png)
vSphere Web Client
![](img/Pasted%20image%2020201201152515.png)
![](img/Pasted%20image%2020201201152531.png)
