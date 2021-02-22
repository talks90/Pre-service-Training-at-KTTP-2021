#虛擬化應用平台部署

# 使用 vCenter 管理虛擬網路
## 網路設定頁面
![](img/Pasted%20image%2020201208142838.png)
![](img/Pasted%20image%2020201208164050.png)
![](img/Pasted%20image%2020201208143322.png)

## 設定網路環境
### vSphere Client 版
#### vSwitch 0
Management Network <--> vmnic 0
==192.168.1xx.101==
![](img/Pasted%20image%2020201208143526.png)
![](img/Pasted%20image%2020201208143618.png)
![](img/Pasted%20image%2020201208143844.png)

#### vSwitch 1
連接埠群組 (port group)
{Production, Test} ---> {vmnic1, vmnic2, vmnic3}

連接埠群組 | 網卡 | use | standby | unuse
-- | --| -- | --| --
 Production | vmnic1, vmnic2, vmnic3 | vmnic1, vmnic2 | vmnic3
 Test | vmnic1, vmnic2, vmnic3 | vmnic3 | vmnic2 | vmnic1

Production
![](img/Pasted%20image%2020201208143904.png)
![](img/Pasted%20image%2020201208143950.png)
![](img/Pasted%20image%2020201208144023.png)
![](img/Pasted%20image%2020201208144132.png)
![](img/Pasted%20image%2020201208144146.png)
![](img/Pasted%20image%2020201208144250.png)
![](img/Pasted%20image%2020201208144336.png)
![](img/Pasted%20image%2020201208144459.png)
![](img/Pasted%20image%2020201208144521.png)

Test
![](img/Pasted%20image%2020201208144706.png)
![](img/Pasted%20image%2020201208144749.png)
![](img/Pasted%20image%2020201208144758.png)
![](img/Pasted%20image%2020201208144834.png)
![](img/Pasted%20image%2020201208144842.png)
![](img/Pasted%20image%2020201208144915.png)
![](img/Pasted%20image%2020201208145105.png)
![](img/Pasted%20image%2020201208145123.png)

#### vSwitch 2
for iSCSI access <--> vmnic4
==192.168.1xx.51==
![](img/Pasted%20image%2020201208151417.png)
![](img/Pasted%20image%2020201208151520.png)
![](img/Pasted%20image%2020201208151620.png)
![](img/Pasted%20image%2020201208151702.png)
![](img/Pasted%20image%2020201208151742.png)
![](img/Pasted%20image%2020201208151751.png)
![](img/Pasted%20image%2020201208151808.png)

#### vSwitch 3
for vMotion <--> vmnic5
==192.168.1xx.52==
![](img/Pasted%20image%2020201208151827.png)
![](img/Pasted%20image%2020201208151841.png)
![](img/Pasted%20image%2020201208151859.png)
![](img/Pasted%20image%2020201208151923.png)
![](img/Pasted%20image%2020201208151955.png)
![](img/Pasted%20image%2020201208152004.png)
![](img/Pasted%20image%2020201208152029.png)

### vSphere Web Client 版
#### vSwitch 0
Management Network <--> vmnic 0
==192.168.1xx.102==
![](img/Pasted%20image%2020201208164140.png)
![](img/Pasted%20image%2020201208164900.png)

#### vSwitch 1
連接埠群組 (port group)
{Production, Test} ---> {vmnic1, vmnic2, vmnic3}

use | standby | unuse | 連接埠群組 | 網卡
-- | --| -- | --| --
vmnic1, vmnic2 | vmnic3| | Production | vmnic1, vmnic2, vmnic3
 vmnic3 | vmnic2 | vmnic1 | Test | vmnic1, vmnic2, vmnic3

Production
![](img/Pasted%20image%2020201208165000.png)
![](img/Pasted%20image%2020201208165045.png)
![](img/Pasted%20image%2020201208165121.png)
![](img/Pasted%20image%2020201208165919.png)
![](img/Pasted%20image%2020201208165941.png)
![](img/Pasted%20image%2020201208170005.png)
![](img/Pasted%20image%2020201208170014.png)
![](img/Pasted%20image%2020201208170026.png)

Test
![](img/Pasted%20image%2020201209100637.png)
![](img/Pasted%20image%2020201209100654.png)
![](img/Pasted%20image%2020201209100712.png)
![](img/Pasted%20image%2020201209100739.png)
![](img/Pasted%20image%2020201209100754.png)
![](img/Pasted%20image%2020201209100829.png)
![](img/Pasted%20image%2020201209105354.png)

#### vSwitch 2
for iScsi access <--> vmnic4
==192.168.1xx.61==
![](img/Pasted%20image%2020201209105550.png)
![](img/Pasted%20image%2020201209105629.png)
![](img/Pasted%20image%2020201209105715.png)
![](img/Pasted%20image%2020201209105805.png)
![](img/Pasted%20image%2020201209105821.png)
![](img/Pasted%20image%2020201209105910.png)
![](img/Pasted%20image%2020201209110010.png)
![](img/Pasted%20image%2020201209110018.png)
![](img/Pasted%20image%2020201209110429.png)

#### vSwitch 3
for vMotion <--> vmnic5
==192.168.1xx.62==
![](img/Pasted%20image%2020201209105550.png)
![](img/Pasted%20image%2020201209105629.png)
![](img/Pasted%20image%2020201209105715.png)
![](img/Pasted%20image%2020201209110208.png)
![](img/Pasted%20image%2020201209110217.png)
![](img/Pasted%20image%2020201209110259.png)
![](img/Pasted%20image%2020201209110330.png)
![](img/Pasted%20image%2020201209110347.png)
![](img/Pasted%20image%2020201209110416.png)
![](img/Pasted%20image%2020201209110545.png)
