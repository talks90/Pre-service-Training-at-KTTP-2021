#Linux 

# 權限應用、程序之觀察與基本管理 

## A. 因為某些緣故，你必須要在 prouser1 的家目錄中建立一個名為 for_student 的目錄，且，這個目錄可以讓 student 這個用戶具有完整權限， 同時 prouser1 也需要具有完整權限，其他人則不具備任何權限！你該如何設計『最小權限』給 prouser1 與 student 用戶？
- 設計最小權限

| 目錄 | 擁有者 | 擁有群組 | 其他 |
| ----------- | ------ | -------- | ---- |
| prouser1 | rwx | --x | --x |
| for_student | rwx    | rwx      | ---  |

![](img/Pasted%20image%2020201107165215.png)變更 /home/prouser1 的權限![](img/Pasted%20image%2020201107172005.png)

## B. 因為某些緣故，你必須要在 prouser1 的家目錄底下建立一個名為 for_read 的目錄，這個目錄可以讓 prouser1 完整讀寫， 而對於其他人來說，仍然具有可瀏覽檔名與讀取內部檔案的權限
- 設計最小權限

| 目錄     | 擁有者 | 擁有群組 | 其他 |
| -------- | ------ | -------- | ---- |
| for_read | rwx    | ---      | r-x  |

![](img/Pasted%20image%2020201107170939.png)
檢查一下成果![](img/Pasted%20image%2020201107172833.png)![](img/Pasted%20image%2020201107173405.png)