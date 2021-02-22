#Linux 

# inode table（inode 表格）的作用

- **每個檔案固定要佔用 1 個 inode** ，每個檔案也僅能佔用 1 個 inode 
- inode 用來記錄檔案屬性及檔案實際資料放在哪幾號 block 內

# inode 的記錄資料

- 該檔案的存取模式(read/write/excute)
- 該檔案的擁有者與群組(owner/group)
- 該檔案的容量
- 該檔案建立或狀態改變的時間(ctime)
- 最近一次的讀取時間(atime)
- 最近修改的時間(mtime)
- 定義檔案特性的旗標(flag)，如 SetUID...
- 該檔案真正內容的指向 (pointer)

# inode 的特色

- 每個 inode 大小均為固定 (可能是 256bytes 或 512bytes，依據檔案系統不同而定。舊的系統則只能到 128 bytes)
- 檔案系統能夠建立的檔案數量與 inode 的數量有關
- 系統讀取檔案時需要先找到 inode，並分析 inode 所記錄的權限與使用者是否符合，若符合才能夠開始實際讀取 block 的內容
