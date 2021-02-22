# 安裝 vsftpd
vsftpd 是用來架 FTP 郵件伺服器的軟體
`dnf -y install vsftpd` ![](img/Pasted%20image%2020201223093155.png)
## 設定檔
這裡會用到的設定檔總共有 1 個
### /etc/vsftpd/vsftpd.conf

==第 12 行：匿名者是否可以登入，預設值是 NO==
`anonymous_enable=NO`

 ==第 22 行：上傳檔案的預設權限遮罩，所以檔案權限會是 644 或 755==
`local_umask=022`

==第 28 行：是否同意讓匿名使用者上傳檔案，建議不要開啟==
`#anon_upload_enable=YES`

==第 52 行：取消註解，開啟 log 檔==
`xferlog_file=/var/log/xferlog`

==第 114 行：是否使用 IPv4 連線，改為 YES==
`listen=YES`

==第 123 行：是否使用 IPv6 連線，改為 NO==
`listen_ipv6=NO`

## 將設定檔目錄的 SELinux 權限重新繫結
`restorecon -Rvv /etc/vsftpd/`

## 打開防火牆
`firewall-cmd --add-service=ftp --permanent`
`firewall-cmd --reload `
![](img/Pasted%20image%2020201223094330.png)

## 啟動 vsftpd 服務 
`systemctl enable --now vsftpd`
![](img/Pasted%20image%2020201223094518.png)

# 測試
登入以後居然可以去查看其他的目錄，這樣會有資安的風險，所以要限制權限 ![](img/Pasted%20image%2020201223094706.png)

# 將使用者的家目錄設成 root，不能瀏覽上層的資料夾
## 修改設定檔
### /etc/vsftpd/vsftpd.conf
==第 100 行：取消註解，讓本機使用者被 chroot，只能在家目錄底下==
`chroot_local_user=YES`

==第 101 行：取消註解，開啟不被 chroot 白名單的功能==
`chroot_list_enable=YES`

==第 103 行：取消註解，設定白名單的檔案路徑==
`chroot_list_file=/etc/vsftpd/chroot_list`

## 建立白名單檔案
`touch /etc/vsftpd/chroot_list`
建立不被 chroot 的使用者帳號列表，**即使沒有任何帳號，此檔案也是要存在！**

## 調整 SELinux FTP 權限
`setsebool -P ftpd_full_access on`

## 將設定檔目錄的 SELinux 權限重新繫結
因為檔案的權限有變動，所以重新繫結
`restorecon -Rvv /etc/vsftpd/`

# 故障排除
怎麼無法登入了！？ ![](img/Pasted%20image%2020201223102648.png)

## 原因
- 查閱說明文件
`man 5 vsftpd.conf`
發現是 chroot 沒有可以寫入的權限
```
       allow_writeable_chroot
              Allow chroot()'ing a user to a directory writable by that user. Note that setting this to  YES  is  poten‐
              tially  dangerous.  For  example,  if  the user creates an 'etc' directory in the new root directory, they
              could potentially trick the C library into loading a user-created configuration file from the /etc/ direc‐
              tory.

              Default: NO
```

## 步驟
- 在 /etc/vsftpd/vsftpd.conf 設定檔內新增設定值
`allow_writeable_chroot=YES`
- 因為 vsftpd.conf 設定檔的內容有變動，所以重新啟動服務
`systemctl restart vsftpd`

可以正常連線囉！ ![](img/Pasted%20image%2020201223103858.png)

# FTP 改為被動模式傳輸
FTP 會用 21 port 來傳輸操作的指令，傳輸的資料則看所選擇的傳輸模式
主動模式用的是 20 port
被動模式用的則是 1024 - 65535 port
![](img/IMG_0619.jpg)
## 修改設定檔
### /etc/vsftpd/vsftpd.conf
==第 42 行：關閉傳輸埠從 20 起算的設定==
`connect_from_port_20=NO`

==開啟被動模式傳輸，並設定傳輸埠最大、最小值==
```
pasv_enable=YES
pasv_max_port=31000
pasv_min_port=30000
```

### 重新啟動 vsftpd
因為 vsftpd.conf 設定檔的內容有變動，所以重新啟動服務
`systemctl restart vsftpd`

## 開啟防火牆被動模式的通訊埠範圍
![](img/Pasted%20image%2020201223152538.png) ![](img/Pasted%20image%2020201223152608.png) ![](img/Pasted%20image%2020201223152717.png) ![](img/Pasted%20image%2020201223152648.png)

## 測試
用被動模式
![](img/Pasted%20image%2020201223153743.png)
來算一下埠號 120 /* 256 + 70 = 30790，跟我們設定一樣喔！
![](img/Pasted%20image%2020201223160058.png)