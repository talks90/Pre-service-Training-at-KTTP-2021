#Linux 

# 檔案權限的探索與確認

## 檢視系統上的 /etc/chrony.keys 這個檔案，並且說明這個檔名的：(1)擁有者 (2)擁有群組 (3)權限幾分？
![](img/Pasted%20image%2020201107111139.png)
1. 擁有者 - root
2. 擁有群組 - chrony
3. 權限分數 - 640 ('r'4, 'w'2, 'x'1)

## 上述檔名的類型是什麼檔？ (一般、目錄、連結或裝置檔等等)
一般檔案 (權限分數前的字元，'-'一般檔案, 'd'目錄, 'l'連結, 'b'裝置, 'c'周邊)

## 請問 chrony 這個用戶對於 /etc/chrony.keys 這個檔案，具有什麼權限 (寫下 r/w/x)
1. 查看 chrony 這個使用者加入了哪些群組![](img/Pasted%20image%2020201107112025.png)
2. chrony 有在 chrony 群組內，所以對 /etc/chrony.keys 這個檔案有 'r' 讀取的權限

## 請問 root 對於這個檔案，又具有什麼權限？
root 是  /etc/chrony.keys 這個檔案的擁有者，有 'r','w' 讀取、修改的權限

# 帳號管理

## 建立名為 theuser1, theuser2, theuser3 三個帳號，三個帳號都加入 thegroup 群組，且密碼都是 thisgroup
1. 新增 theuser1, theuser2, theuser3 使用者![](img/Pasted%20image%2020201107113435.png)
2. 加入 thegroup 群組![](img/Pasted%20image%2020201107114949.png)
3. 設定使用者密碼為 thisgroup![](img/Pasted%20image%2020201107115447.png)

> 註：不知大家有沒有嘗試要用 {1..3} 的方式來大量新增使用者，可惜 useradd 不允許大量新增；那如果在需要大量新增使用者的情況下怎麼辦？！

### 大量新增使用者、設定密碼
1. 先新增一個一般檔案，按 /etc/passwd 的格式編輯要新增的使用者資料（ 這裡檔名取為 user.txt ）；要注意每個使用者的使用者名稱、UID、宿主目錄都不可以相同，其中密碼欄可以留做空白或輸入x號![](img/Pasted%20image%2020201107124053.png)
2. 以 **root** 的身份執行` newusers `來建立使用者，並匯入剛剛建立的使用者檔案![](img/Pasted%20image%2020201107124631.png)
3. 為了方便下一步的密碼轉換工作，先執行` pwunconv `指令，把 /etc/shadow 產生的 shadow 密碼解碼，然後回寫到 /etc/passwd 中，並將/etc/shadow的shadow密碼欄刪掉![](img/Pasted%20image%2020201107124756.png)
4. 新增一個一般檔案，按照` username:passwd `的格式編輯使用者密碼（ 這裡檔名取為 passwd.txt ）![](img/Pasted%20image%2020201107124956.png)
5. 以root身份執行` chpasswd `來變更密碼，並匯入剛剛建立的使用者密碼![](img/Pasted%20image%2020201107125438.png)
6. 執行` pwconv `再將密碼回寫到 /etc/shadow ![](img/Pasted%20image%2020201107125720.png)
7. 這樣就完成大量使用者的建立了；可以到 /home 下檢查這些使用者家目錄的權限權設定是否都正確，並登入使用者驗證密碼是否正確

## 觀察 theuser1 的 UID 與 GID 規劃，記下來他是幾號喔！
![](img/Pasted%20image%2020201107130406.png)
## 單純使用 userdel 不要加上 -r 來刪除 theuser1 這個帳號
![](img/Pasted%20image%2020201107130723.png)

## 現在，請使用查詢到的 UID 與 GID 資料，來重新建置 theuser1 這個帳號，且這個帳號可以使用原本的家目錄等資源
1. 修改 /etc/passwd ![](img/Pasted%20image%2020201107130830.png)![](img/Pasted%20image%2020201107131112.png)
2. 修改 /etc/group ![](img/Pasted%20image%2020201107131358.png)![](img/Pasted%20image%2020201107131552.png)
3. 別忘了密碼![](img/Pasted%20image%2020201107131706.png)

# 權限管理

## 建立一個名為 /srv/thegroup 目錄，這個目錄可以讓 thegroup 的成員在裡面做任何事情
![](img/Pasted%20image%2020201107132439.png)

## /usr/local/sbin/myif 複製來自 /sbin/ifconfig 檔案，這個檔案只有 thegroup 成員可以執行，其他人無任何權限
![](img/Pasted%20image%2020201107133209.png)
切換不同使用者測試一下![](img/Pasted%20image%2020201107133546.png)![](img/Pasted%20image%2020201107133806.png)
