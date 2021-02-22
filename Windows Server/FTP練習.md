#Windows伺服器環境規劃建置 #FTP 

# 題目 1
## 要求
1. 不隔離使用者
2. 有專屬目錄

### 使用者
- HRU
- RDU
- HRM

### 架構
```
C
|-- company
 	|-- ftp
 	 	|-- HRU
 	 	|	  HRU.txt
 	 	|-- RDU
 	 	|	  RDU.txt
 	 	|-- default			// 保留字
 	 	 	  default.txt
```

## 設定
![](../img/Pasted%20image%2020201027150753.png)

## 觀察結果

使用者 | 登入資料夾
--- | ---
HRU | HRU
RDU | RDU
HRM | defult
anonymous | Default
ABC |  X


## 故障排除
1. 連線時只有 anonymous 可以登入，其他使用者都不行![](../img/Pasted%20image%2020201027145859.png)
- 檢查 FTP 驗證設定對不對![](../img/Pasted%20image%2020201027145336.png)![](../img/Pasted%20image%2020201027145620.png)

2. 無法存取家目錄![](../img/Pasted%20image%2020201027150233.png)
- 檢查家目錄的設定跟實體目錄結構![](../img/Pasted%20image%2020201027150416.png)![](../img/Pasted%20image%2020201027150541.png)看起來都沒問題
- 檢查「驗證和授權資訊」原來是我這裡之前在建立 FTP 站台第 2 步時設定成「匿名使用者」了![](../img/Pasted%20image%2020201026150414.png)，改成「所有使用者」就可以順利完成練習囉~![](../img/Pasted%20image%2020201027151545.png)
- 小眉角，服用前重新啟動服務，就不用重新開機了![](../img/Pasted%20image%2020201027151836.png)

# 題目 2 
## 要求
1. 隔離使用者
2. 有專屬目錄

### 使用者
- HRU
- RDU
- HRM

### 架構
```
C
|-- company
 	|-- ftp
		|-- localuser         	// 保留字
 	 		|-- HRU
 	 		|	  HRU.txt
 	 		|-- RDU
 	 		|	  RDU.txt
 	 		|-- public			// 保留字
	 	 	 	  public.txt
```
> 跟上一題不一樣的是這裡的預設目錄改名為 public

## 設定
![](../img/Pasted%20image%2020201027161151.png)

## 觀察結果

使用者 | 登入資料夾
--- | ---
HRU | HRU
RDU | RDU
HRM | X
anonymous | public
ABC |  X

如果把 public 資料夾名稱改回 default 會怎樣呢？

使用者 | 登入資料夾
--- | ---
HRU | HRU
RDU | RDU
HRM | X
anonymous | X
ABC |  X

### 結論
在不同的設定下，預設目錄是不同的，服用時請詳閱公開說明書

# 題目 3
## 要求
1. 隔離使用者
2. 有專屬目錄
3. 啟用全域虛擬目錄

### 使用者
- HRU
- RDU
- HRM

### 架構
```
C
|-- company
 	|-- ftp
		|-- PublicDATA		// 全域虛擬目錄
		|-- localuser
 	 		|-- HRU
 	 			|-- PDATA	// 虛擬目錄，指向 PublicDATA
 	 		|-- RDU
 	 			|-- PDATA	// 虛擬目錄，指向 PublicDATA
 	 		|-- public
 	 			|-- PDATA	// 虛擬目錄，指向 PublicDATA
```

## 設定
全域虛擬目錄，類似共用區的概念!
1. 勾選「使用者名稱實體目錄（啟用全域虛擬目錄）」![](../img/Pasted%20image%2020201028144604.png)
2. 在 ftp 站台上選「新增虛擬目錄」![](../img/Pasted%20image%2020201028144908.png)完成「別名」及「實體路徑」的設定![](../img/Pasted%20image%2020201028151545.png)
3. 接著還要到ftp 站台的「FTP 瀏覽目錄」內![](../img/Pasted%20image%2020201028150511.png)勾選顯示「虛擬目錄」![](../img/Pasted%20image%2020201028150749.png)，完成後別忘了「套用」喔~
## 觀察結果

使用者 | 登入資料夾
--- | ---
HRU | HRU, PDATA
RDU | RDU, PDATA
HRM | X
anonymous | public, PDATA
ABC |  X

# 題目 4
## 要求
1. 隔離使用者
2. 有專屬目錄
3. 停用全域虛擬目錄

### 使用者
- HRU
- RDU
- HRM

### 架構
```
C
|-- company
 	|-- ftp
		|-- HDATA			// 區域虛擬目錄
		|-- RDATA			// 區域虛擬目錄
		|-- PuDATA			// 區域虛擬目錄
		|-- localuser
 	 		|-- HRU
 	 			|-- HRDATA	// 虛擬目錄，指向 HDATA
 	 		|-- RDU
 	 			|-- RDDATA	// 虛擬目錄，指向 RDATA
 	 		|-- public
 	 			|-- pubDATA	// 虛擬目錄，指向 PuDATA
```

## 設定
1. 勾選「使用者名稱實體目錄（停用全域虛擬目錄）」![](../img/Pasted%20image%2020201028155953.png)
2. 在 localuser 下各使用者目錄下把虛擬目錄建起來![](../img/Pasted%20image%2020201028162003.png)
## 觀察結果

使用者 | 登入資料夾
--- | ---
HRU | HRU, HRDATA
RDU | RDU, PDDATA
HRM | X
anonymous | public, pubDATA
ABC |  X

# 題目 5
## 要求
### 架構
```
C
|-- company
 	|-- ftp
		|-- RDF
		|-- HDF
 		|-- public
```

### 使用者
- HRU / 123
- HRM / 1234
- RDU / 456
- RDM / 4567

### 權限表
\  | HRF | RDF | public 
--- | ---| ---| ---
HRU | R | X | R
HRM |R/W | R | R/W
RDU | X | R | R
RDM |  R | R/W | R/W

## 設定
1. 調整 FTP 使用者隔離的設定![](../img/Pasted%20image%2020201029145122.png)
2. 調整各資料夾的 FTP 授權規則![](../img/Pasted%20image%2020201029153009.png)![](../img/Pasted%20image%2020201029145235.png)![](../img/Pasted%20image%2020201029145254.png)
3. 在有寫入權限的資料夾內可以上傳檔案就 OK 囉~
> 註：資料夾安全性的權限有一個 CREATOR OWNER，會限制只有檔案創造人才有完全權限；用來當防誤刪的機制


# 課後評量 T5.7.2

課後評量時，忽然發現 ftp 運作不正常
- 現象
檔案不斷傳輸、簽證不過、傳輸的通訊埠不斷更改
![](img/Pasted%20image%2020201201084838.png)
- 嘗試失敗
	- 重新做 CA 憑證 - 因為簽證一直不過，所以先試試是不是憑證的問題
	- 關閉防火牆 - 通訊埠一直跳，會不會是防火牆？？？
- 解決作法
在伺服器上安裝 KB2919355, KB2919442 更新檔
![](img/Pasted%20image%2020201201084421.png)
終於解決了
![](img/Pasted%20image%2020201201083536.png)
