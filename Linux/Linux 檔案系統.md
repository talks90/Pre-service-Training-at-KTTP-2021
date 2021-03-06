#Linux 

# 認識 Linux 檔案系統

- 主要支援 EXT2 家族 (目前新版為 EXT4) 以及 XFS 大型檔案系統兩種
- 其中 XFS 相當適合大容量磁碟， 格式化的效能非常快，是現在的主流
- 但無論哪種檔案系統，都必須要符合 inode 與 block 等檔案系統使用的特性

## 磁碟檔名與磁碟分割

### 磁碟分割

- 整顆磁碟必須要經過分割之後，作業系統才能夠讀取分割槽內的檔案系統
- 磁區(Sector)為最小的物理儲存單位，目前主要有 512bytes 與 4K 兩種格式
- 分割的最小單位可能是磁柱 (cylinder) 也可能是磁區 (sector)，這與分割工具有關
- 磁碟分割表主要有兩種格式，分別是 MBR ( MSDOS 建立的格式 ) 與 GPT 分割表
- [[Linux/MBR 分割表]]中，第 1 個磁區最重要，裡面有
	1. 主要開機區(Master boot record, MBR) - 446 bytes 
	2. 分割表(partition table) - 64 bytes
- [[Linux/GPT 分割表]]除了分割數量擴充較多之外，支援的磁碟容量也可以超過 2TB
- 近年來 SSD 或 M2 插槽的固態硬碟已經使用的越來越廣泛，而這些固態硬碟並沒有所謂的磁頭、磁柱與磁軌，因此， 分割工具漸漸則以磁區 (sector) 為單位去進行分割的行為了！這樣對於傳統磁碟或固態硬碟來說，就會顯的一致

### 磁碟檔名

- 實體磁碟的檔名 - /dev/sd\[a-p] 
- 虛擬磁碟的檔名 - /dev/vd\[a-p] ；透過 virtio 模組加速
- 磁碟陣列的檔名 - /dev/md126 ；磁碟陣列可以使用軟體跟 Intel 主機板上面內建等方式來組成
- 由於虛擬機器的環境中，大部分磁碟的容量還是小於 2TB 的條件，因此傳統的 MSDOS 分割表還是有其存在的需求的

## Linux 的 EXT2 檔案系統

- 新的作業系統在規劃檔案系統時，一般檔案會有屬性（如權限、時間、身份資料紀錄等），以及實際資料的紀錄， 同時整個檔案系統會紀錄全部的資訊
- 檔案系統利用 [[Linux/superblock]], [[Linux/inode]], [[Linux/data block]] 來記錄檔案的全部資訊![](img/Pasted%20image%2020201110201635.png)

### 檔案讀取流程

1. 讀到檔案的 inode 號碼
2. 由 inode 內的權限設定判定使用者能否存取此檔案
3. 若能讀取則開始讀取 inode 內所記錄的資料放置於哪些 block 內
4. 讀出 block 內的資料，組合起來成為一個檔案的實際內容

### 新增檔案流程

1. 有寫入檔案的需求時，先到 metadata 區找到沒有在用的 inode 號碼
2. 到該 inode 號碼內，將所需要的權限與屬性相關資料寫入，然後在 metadata 區規範該 inode 為使用中，且更新 superblock 資訊
3. 到 metadata 區找到沒有在用的 block 號碼，將所需要的實際資料寫入 block 當中，若資料量太大，則繼續到 metadata 當中找到更多的未使用中的 block 號碼，持續寫入，直到寫完資料為止
4. 同步更新 inode 的紀錄與 superblock 的內容

### 刪除檔案流程

1. 將該檔案的 inode 號碼與找到所屬相關的 block 號碼內容抹除
2. 將 metadata 區域的相對應的 inode 與 block 號碼規範為未使用
3. 同步更新 superblock 資料

## 目錄與檔名目錄

當使用者在 Linux 下的檔案系統建立一個目錄時，檔案系統會分配一個 inode 與至少一塊 block 給該目錄
- inode 記錄該目錄的相關權限與屬性，並記錄分配到的那塊 block
- block 記錄在這個目錄下的檔名與該檔名佔用的 inode 號碼資料

## ln 連結檔的應用

![](img/Pasted%20image%2020201112225440.png)如果仔細看，其實會發現目錄的預設連結數 (使用 ls -l 觀察檔名的第二個欄位) 為 2，這是因為每個目錄底下都有 . 這個檔名，而這個檔名代表目錄本身，因此目錄本身有兩個檔名連結到同一個 inode 號碼上， 故連結數至少為 2 
同時每個目錄內都有 .. 這個檔名來代表父目錄，因此每增加一個子目錄，父目錄的連結數也會加 1
連結數增加後，若檔名刪除時，其實 inode 號碼並沒有被刪除，因此這個『實體連結』的功能會保護好原本的檔案資料




## 檔案系統的掛載


# 檔案系統管理
## 建立分割
## 建立檔案系統 (磁碟格式化)
## 檔案系統的掛載/卸載
## 開機自動掛載

# 開機過程檔案系統問題處理
## 檔案系統的卸載與移除
## 開機過程檔案系統出錯的救援