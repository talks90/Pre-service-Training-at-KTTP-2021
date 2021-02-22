<font color=red>P.S 紅字部分可隨意取代</font>

# 設定主機名稱

Switch(config)#hostname <font color=red>主機名稱</font>

主機名稱(config)#

# 設定密碼

---
## enable密碼

Switch(config)#enable password level  <font color=red>1 密碼</font>
> //密碼最少4個字元,最多8個字元,level為1-15

Switch(config)#enable secret <font color=red>密碼</font>
> //加密模式密碼,如有設定,會蓋過enable密碼  

---
## console密碼

Switch(config)#line console 0

Switch(config-line)#password <font color=red>密碼</font>   

Switch(config-line)#login

Switch(config-line)#exec-timeout 0
> //閒置登出

Switch(config-line)#logging synchronous
> //訊息同步

---
## telnet (vty) 密碼

Switch(config)#line vty 0 15
> // 0-15 共有16條vty線路

Switch(config-line)#password <font color=red>密碼</font>

Switch(config-line)#login

---

# 介面設定

Switch(config)#int FastEthernet0/12

Switch(config-if)#description <font color=red>介面描述</font>
> //在sh int 及 sh run int 內可看到

Switch(config-if)#duplex auto
> //設定雙工模式,預設auto

Switch(config-if)#speed auto
> //設定速度,預設auto

Switch(config-if)#^z

Switch#sh running-config 
!
interface FastEthernet0/12
 description <font color=red>介面描述</font>
!

# 啟動介面

Switch(config)# int <font color=red>介面</font>
> //介面為e0,S1/0,fa0/15,atm2/3.14,...等

Switch(config-if)#no shutdown
> //啟動介面

Switch(config-if)#\^z
> //ctrl+Z 表示快速寫入config

# ip 設定

Switch(config)#interface vlan <font color=red>1</font>

Switch(config-if)#ip address 192.168.0.250 255.255.255.0
> //設定ip及Subnet Mask

Switch(config-if)#no shutdown 

Switch(config-if)#exit

Switch(config-if)#ip default-gateway  192.168.0.254
> //跨網段管理

Switch(config-if)#\^z

# 設定SSH

Switch#configure terminal 

Switch(config)#ip domain-name example.com

Switch(config)#crypto key generate rsa 

Switch(config)#username netadmin password netadmin

Switch(config)#ip ssh version 2

Switch(config)#line vty 0 15

Switch(config-line)#login local

Switch(config-line)#transport input ssh telnet

# 安全設定

1.Port security 只針對Access mode的Port設定

2.enable port security

3.(Optional)設定最大MAC數量,預設 1 show不會顯示,Cisco設備預設值通常不顯示

4.違規時switch的動作violation{<font color=blue>shutdown預設 | restrict | protect</font>}

5.1~4只防阻MAC Flooding攻擊,每一個port只能有1個MAC通過,但還不能防止非法MAC接到設備上,把合法 MAC寫到table,只要MAC有錯port就會 shutdown

Switch#show mac-address-table
> //在Switch上查看MAC-Address-Table

<font color=green>將介面啟用Port-Security,指定只學習一筆MAC,使用Sticky方式學習MAC,並設定違規處理方式為Shutdown</font>

Switch(config)#interface fastEthernet <font color=red>0/18</font>
> //指定介面

Switch(config-if)#switchport mode access
> //將Port改成Access Mode

Switch(config-if)#switchport port-security
> //啟用Port Security

Switch(config-if)#switchport port-security maximum <font color=red>1</font>
> //設定最大的MAC數量

Switch(config-if)#switchport port-security mac-address sticky 

Switch(config-if)#switchport port-security violation shutdown
> //預設,處理err-disabled

或

Switch(config-if)#switchport port-security violation restrict
> //合法MAC接回就會通

---

Switch(config-if)#do sh port-security
> //檢查有哪些Port啟用Port-security

Switch(config-if)#do sh port-security address
> //檢查有哪些Port有sticky或手動輸入MAC Address

Switch(config-if)#do sh port-security interface fa 0/18
> //檢查Fa0/18的Port-security的詳細設定

Switch(config-if)#do sh int fa 0/18
> //檢查Fa0/18的介面狀態(注意err-disabled)

---
## range 多個Port設定

Switch(config)#interface range fastEthernet 0/4 - 7 , fastEthernet 0/9 - 15

Switch(config-if-range)#switchport mode accesss       

---

將Fa0/18接另一台PC,因為Fa0/18有啟用Port-security,並將之前自動學習到的MAC以Sticky記錄,檢查

Fa0/18的介面狀態會出現 <font color=red>err-disabled</font>

解決方法

Switch#sh int fa 0/18

FastEthernet0/18 is down, line protocol is down (<font color=red>err-disabled</font>)

Switch#sh run | begin FastEthernet0/18

!

interface FastEthernet0/18

 switchport mode access

 switchport port-security

 switchport port-security mac-address sticky 

<font color=green>switchport port-security mac-address sticky 0020.E22D.9B05</font>
> //此行為出問題的MAC address

!

Switch#conf t

Switch(config)#int fa 0/18

Switch(config-if)#no switchport port-security mac-address sticky 0020.E22D.9B05

Switch(config-if)#do sh int fa 0/18

FastEthernet0/18 is down, line protocol is down (err-disabled)

Switch(config-if)#shutdown 

Switch(config-if)#no shutdown 

Switch#sh interfaces status
> //檢查所有界面的狀態

# 停用網域名稱反查

Switch(config)#no ip domain-lookup

# 組態設定

將Switch的IOS與startup-config複製到TFTP

Switch#sh flash

Switch#copy flash tftp

Switch#copy running-config startup-config
> //設定寫入NVRAM(組態檔)

Switch#copy startup-config tftp

---               

Switch#erase startup-config
> //刪除啟動組態檔

Switch#delete vlan.dat

Switch#reload

或

Switch#delete nvram
> //刪除啟動組態檔

# CDP相關設定

---
## 查詢鄰近設

Switch#sh cdp neighbors  (Device ID =>ro)

Switch#sh cdp entry router

---
## 將Switch的介面CDP功能關閉

Switch(config)#int fa <font color=red>0/5</font>
Switch(config-if)#no cdp enable

---
## 將設備dcp功能關閉

lab2(config)#no cdp run