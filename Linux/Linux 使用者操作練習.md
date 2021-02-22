#Linux 

# 最小權限設定
假如用戶想在『 /dir1/file1 』這個檔案與『 /dir2 』這個目錄之間進行如下的動作時，用戶應該具有哪些『最小權限』才能運作？

|       操作動作        | /dir1 | /dir1/file1 | /dir2 |                   重點                    |
|:---------------------:|:-----:|:-----------:|:-----:|:-----------------------------------------:|
|    讀取 file1 內容    |  r-x  |     r--     |  ---  | 要能夠進入 /dir1 才能讀到裡面的文件資料！ |
|    修改 file1 內容    |  r-x  |     rw-     |  ---  |    能夠進入 /dir1 且修改 file1 才行！     |
|    執行 file1 內容    |  --x  |     r-x     |  ---  |   能夠進入 /dir1 且 file1 能運作才行！    |
|    刪除 file1 檔案    |  -wx  |     ---     |  ---  |  能夠進入 /dir1 具有目錄修改的權限即可！  |
| 將 file1 複製到 /dir2 |  --x  |     ---     |  -wx  | 要能夠讀 file1 且能夠修改 /dir2 內的資料  |
| 將 file1 移動到 /dir2 |  -wx  |     ---     |  -wx  |           兩個目錄均需要被更改            |

# 測試不同權限下的系統操作結果 

## 請使用 root 的身份建立底下的檔案與權限
```
drwxrwxr-x  root root /dev/shm/unit05/
drwxr-xr--  root root /dev/shm/unit05/dir1/
-rw-r--r--  root root /dev/shm/unit05/dir1/file1 (複製來自 /etc/hosts)
drwxr-x--x  root root /dev/shm/unit05/dir2/
-rw-r--r--  root root /dev/shm/unit05/dir2/file2 (複製來自 /etc/hosts)
drwxr-xr-x  root root /dev/shm/unit05/dir3/
-rw-rw-rw-  root root /dev/shm/unit05/dir3/file3 (複製來自 /etc/hosts)
drwxrwxrwx  root root /dev/shm/unit05/dir4/
-rw-------  root root /dev/shm/unit05/dir4/file4 (複製來自 /etc/hosts)
```

1. 建立目錄 ![](img/Pasted%20image%2020201107150158.png)
2. 複製檔案 ![](img/Pasted%20image%2020201107151238.png)
3. 設定權限 ![](img/Pasted%20image%2020201107151956.png)
4. 檢查一下 ![](img/Pasted%20image%2020201107152236.png)

## 底下請使用 student 的身份進行各個工作

### 請使用 ls -l /dev/shm/unit05/dir\[1-4\] 依據輸出的結果說明為何會產生這些問題？
![](img/Pasted%20image%2020201107153232.png)

### 請使用 ls -l /dev/shm/unit05/dir1/file1 ，依序將上述的檔名由 dir1/file1 ~ dir4/file4 執行，依據產生的結果說明為何會如此？
![](img/Pasted%20image%2020201107154057.png)

### 請使用 vim /dev/shm/unit05/dir1/file1 ~ vim /dev/shm/unit05/dir4/file4，嘗試儲存 (或強制儲存)，說明為何可以/不可以儲存？
![](img/Pasted%20image%2020201107154638.png)![](img/Pasted%20image%2020201107154917.png)![](img/Pasted%20image%2020201107155156.png)![](img/Pasted%20image%2020201107155451.png)
