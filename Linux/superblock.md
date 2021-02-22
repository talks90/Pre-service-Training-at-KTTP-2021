#Linux 

# Superblock（超級區塊）的作用

superblock 為整個檔案系統的總結資訊處，要讀取檔案系統一定要從 superblock 讀起

# superblock 的記錄資料

- block 與 inode 的總量
- 未使用與已使用的 inode / block 數量
- block 與 inode 的大小 (block 為 1, 2, 4K，inode 為 128bytes, 256bytes 或 512bytes)
- filesystem 的掛載時間、最近一次寫入資料的時間、最近一次檢驗磁碟 (fsck) 的時間等檔案系統的相關資訊
- 一個 valid bit 數值，若此檔案系統已被掛載，則 valid bit 為 0 ，若未被掛載，則 valid bit 為 1
