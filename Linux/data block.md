#Linux 

# data block（資料區塊）的作用

- 檔案實際的資料存放在 data block 上面
- 每個 block 都有號碼，提供給 inode 記錄資料放在哪個 block
- 原則上，格式化後 block 的大小與數量就不能再改了（除非重新格式化）
- 每個 block 內最多只能夠放置一個檔案的資料
- 如果單一檔案大小 > block 容量，會佔用多個 block
- 若單一檔案大小 < block 容量，該 block 的剩餘容量也不能再被使用（會浪費磁碟空間）
