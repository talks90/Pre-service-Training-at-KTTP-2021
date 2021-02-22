#Windows伺服器環境規劃建置 #DNS

# 題目 1 - 架設 DNS SVR 
## 要求
1. http://SVR_IP/ 連到**測試網頁**
2. http://www.test.com/ 也可以連到**測試網頁**

## 安裝 DNS SVR 服務
1. 新增角色及功能 > 網頁伺服器 > DNS 伺服器![](../img/Pasted%20image%2020201102154958.png)
2. 完裝完畢之後就可以在管理工具看到 DNS 出現囉![](../img/Pasted%20image%2020201102155100.png)

## 設定 DNS
- 設定之前先搞清楚什麼是區域名稱，什麼是主機名稱！基本上Domain Name 是一個分層級的名稱對應系統；比如說，www.xyz.com.tw 的網址，tw 由 twnic 來管理，com 再由 twnic 下面的 DNS 主機來管，而 xyz 公司則要去申請在 com 下面才可以被查詢到![](../img/Pasted%20image%2020201102160439.png)

1. 打開 DNS 管理員，新增一個區域![](../img/Pasted%20image%2020201102162614.png)
	- 正向對應區域 - 指將 IP 轉成 Domain Name
	- 反向對應區域 - 指將 Domain Name 轉成  IP
2. 正向對應區域 > 新增區域![](../img/Pasted%20image%2020201102162946.png)
3. 設定區域名稱![](../img/Pasted%20image%2020201102164059.png)
4. 選取主要區域![](../img/Pasted%20image%2020201102163038.png)
	- 虛試常式區域 - 類似快取的概念
5. 建立 DNS 檔![](../img/Pasted%20image%2020201102163505.png)![](../img/Pasted%20image%2020201102163541.png)
6. 完成 DNS 架設囉![](../img/Pasted%20image%2020201102163632.png)
7. 接著立馬把我們的主機加到 DNS 內吧！!![](../img/Pasted%20image%2020201102164157.png)![](../img/Pasted%20image%2020201102164301.png)
	- PTR - 如果有建反向對應區域的話，會自動新增反向的連結
8. 別忘了，用戶端記得設定 DNS，不然要找誰轉？！![](../img/Pasted%20image%2020201102164424.png)

# 題目 2 - DNS + IIS 綜合練習
## 架構
```
C
|-- company
 	|-- web
 	 	|-- public
 	 	|	  index.htm	(外部公告)
 	 	|-- private
 	 	 	  index.htm	(內部消息)

```

## 要求 - 第 1 階段
1. 僅可使用 1 個站台
2. 輸入
	- http://[SVR IP]/in   --> 內部消息
	- http://[SVR IP]/out --> 外部公告

## 設定
- 利用「新增虛擬目錄」設定「別名」的方式來完成![](../img/Pasted%20image%2020201104221550.png)![](../img/Pasted%20image%2020201104221836.png)

## 要求 - 第 2 階段
1. 使用 2 個站台，同一個 ip, port number
2.  輸入
	- http://www1.test.com	--> 內部消息
	- http://www2.test.com	--> 外部公告

## 設定
1. 新增 2 個站台，一個設 www1 繫結到 private，另一個設 www2 繫結到 public![](../img/Pasted%20image%2020201104222206.png)![](../img/Pasted%20image%2020201104223031.png)
2. 設定 2 個 DNS 連結，同樣有 www1, www2，並且都連到同一個 SVR IP![](../img/Pasted%20image%2020201104222341.png)![](../img/Pasted%20image%2020201104223146.png)
3. 完成囉![](../img/Pasted%20image%2020201104223356.png)![](../img/Pasted%20image%2020201104223301.png)

# 題目 3 - DNS + FTP 綜合練習
## 要求
- 輸入
	ftp.test.com 登入

### 架構
```
C
|-- company
 	|-- ftp
 	 	|-- public
 	 	|-- HRF
```

### 使用者
- HRU / 123
- HRM / 1234

### 權限表
\  | HRF | public 
--- | ---| ---
HRU | R/W | R
HRM |R/W | R/W

# 題目 4 - DNS + FTP 綜合練習
## 要求
- 輸入
	- ftp1.test.com --> ftp1
	- ftp2.test.com --> ftp2

### 架構
```
C
|-- company
 	|-- ftp
 	 	|-- ftp1
 	 	|-- ftp2
```

## 設定
- 設定的部分跟前面都大同小異
- 進入 FileZilla 後，注意使用者的部分表示方式不同
![](img/Pasted%20image%2020201104140025.png)


# 題目 5 - DNS + FTP + SSL 綜合練習
## 要求
- 以 ftps.test.com 進行連線登入

### 架構
```
C
|-- company
 	|-- ftp
 	 	|-- ftps
 	 		  fpts.txt
			  	:
				:
```

## 設定
1. 先申請一個新的憑證，並到站台內加入 FTP SSL 認證的要求![](../img/Pasted%20image%2020201104161012.png)
2. 選取剛剛申請下來的憑證![](../img/Pasted%20image%2020201104161305.png)
3. 用戶端將憑證下載下來之後，匯入 mms ![](../img/Pasted%20image%2020201104161601.png)
4. FileZilla 的連線設定，我選擇用隱含式 TLS 來進行連線![](../img/Pasted%20image%2020201104162159.png)
5. 出現憑證的題示框囉~![](../img/Pasted%20image%2020201104162409.png)

## 錯誤碼
- 534 Local policy on server does not allow TLS secure connections
> 解法 - IIS根結點的FTP SSL Setting 要先設定好證書
- 530 Valid Hostname is expected
> 解法 - 用戶端的帳號前面要加上 "domain|"

## 故障排除
- 現象 - 之前架設的 CA 伺服器無法使用
- 解決方法 - 將原有的 CA 角色移除後再重新安裝，讓它跟 Default Web Site 再重新連結