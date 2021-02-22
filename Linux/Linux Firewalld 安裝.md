# 安裝 firewalld
firewalld 是用來架防火牆的軟體、firewall-config 是設定防火牆的軟體、firewall-applet 是防火牆圖形化介面的軟體
`dnf -y install firewalld firewall-config firewall-applet` ![](img/Pasted%20image%2020201223135452.png) ![](img/Pasted%20image%2020201223135511.png) ![](img/Pasted%20image%2020201223135532.png)

## 設定
之前我們都是用指令增加 firewalld 的規則，這裡我們用圖形化介面的軟體來做設定
開啟防火牆軟體 ![](img/Pasted%20image%2020201223135800.png) ![](img/Pasted%20image%2020201223135822.png)
### 開啟防火牆
- 特定服務
![](img/Pasted%20image%2020201223140429.png) ![](img/Pasted%20image%2020201223140522.png) ![](img/Pasted%20image%2020201223140602.png)
勾選完設定就生效了 ![](img/Pasted%20image%2020201223140738.png)

- 特定通訊埠
![](img/Pasted%20image%2020201223140914.png) ![](img/Pasted%20image%2020201223140950.png) ![](img/Pasted%20image%2020201223141019.png)
設定完成 ![](img/Pasted%20image%2020201223141134.png)
 
 - 修改服務的預設通訊埠
![](img/Pasted%20image%2020201223150023.png)
> 這裡修改的設定，設定檔會存放在 /etc/firewalld/services/
> ![](img/Pasted%20image%2020201223150633.png)

這裡要注意的事，這些設定是**執行時期**有效而已，重新開機之後，設定就回複原來的設定值了
那要如果將設定值存到永久設定呢？ ![](img/Pasted%20image%2020201223143245.png)

# 測試
這裡來改 ssh 的通訊埠，測試我們剛剛修改的狀況

## 修改設定檔
### /etc/ssh/sshd_config
==第 17 行：取消註解，設定成要使用的埠號==
`Port 56789`

## 開啟 SELinux 的埠號
`semanage port -a -t ssh_port_t -p tcp 56789`

## 重新啟動 sshd 服務
`systemctl restart sshd`

## 測試
### 檢查 sshd 現在監聽的埠號
`ss -nlp | grep ssh`
![](img/Pasted%20image%2020201223144828.png)

### 連線測試
`ssh -p 56789 student@192.168.43.131`
成功連線了 ![](img/Pasted%20image%2020201223145130.png)

## 打開防火牆
`firewall-cmd --add-service=ftp --permanent`
`firewall-cmd --reload`
![](img/Pasted%20image%2020201223094330.png)