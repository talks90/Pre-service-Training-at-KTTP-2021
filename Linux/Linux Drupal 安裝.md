# 安裝 Drupal

找到適用的版本
![](img/Pasted%20image%2020201216133912.png)
下載 Drupal 核心的壓縮檔
`curl -L "https://ftp.drupal.org/files/projects/drupal-8.9.11.tar.gz" -o /usr/share/nginx/www/drupal.tar.gz`
解壓縮並更名
`tar zxvf /usr/share/nginx/www/drupal.tar.gz && mv /usr/share/nginx/www/drupal-8.9.11/ /usr/share/nginx/www/drupal`

## 安裝正體中文
語言包的位置是在 Drupal 工作目錄下的 `sites/default/files/translations` 資料夾內，因為預設沒有 `files/translations` 資料夾，所以先來建立
`mkdir -p /usr/share/nginx/www/drupal/sites/default/files/translations`
下載正體中文語言包，注意要下載相對應的版本
![](img/Pasted%20image%2020201216134920.png)
`curl -L "https://ftp.drupal.org/files/translations/all/drupal/drupal-8.9.11.zh-hant.po" -o /usr/share/nginx/www/drupal/sites/default/files/translations/drupal-8.9.11.zh-hant.po`

## 修改 Nginx 的設定檔
更改 Nginx 設定檔裡的 root 資料夾位置
`vim /etc/nginx/conf.d/www.conf`
重置 Nginx 設定檔
`nginx -s reload`
可以進到 Drupal 的設定畫面囉
![](img/Pasted%20image%2020201216140634.png)

## Drupal 的初始設定
- 選擇語言
![](img/Pasted%20image%2020201216140836.png)

- 標準安裝
![](img/Pasted%20image%2020201216141056.png)

- 檢查系統需求
偵測到問題了
![](img/Pasted%20image%2020201216141304.png)
先改資料夾的擁有者，讓 nginx 有讀寫的權限
`chown nginx.nginx /usr/share/nginx/www/drupal/sites/default/files`
檢查 SELinux 的設定值
`ls -Zda /usr/share/nginx/www/drupal/sites/default/files`
![](img/Pasted%20image%2020201216142057.png)
我們需要讓 `/usr/share/nginx/www/drupal/sites/default` 下的所有檔案都有 `httpd_sys_rw_content_t` 讀寫的權限
![](img/Pasted%20image%2020201216142522.png)
`semanage fcontext -a -t httpd_sys_rw_content_t '/usr/share/nginx/www/drupal/sites/default/files(/.*)?'`
重新繫結一下
`restorecon -Rvv /usr/share/nginx/www/drupal/sites/default/files/`
`chown -R nginx:nginx /usr/share/nginx/html/phpMyAdmin`
下一個問題 - 沒有 setting.php 檔，解決方式已經寫在上面了
![](img/Pasted%20image%2020201216143305.png)
更改 setting.php 檔的擁有者，讓 nginx 有讀寫的權限
![](img/Pasted%20image%2020201216143550.png)

- 設定資料庫
先回 mariadb 建立 drupal 使用者，讓 drupla 有資料庫可以用
![](img/Pasted%20image%2020201216144115.png)
帳號 - drupal 密碼 db@123
![](img/Pasted%20image%2020201216144455.png)
建好了
![](img/Pasted%20image%2020201216144618.png)
回 Drupal 填入相關設定
![](img/Pasted%20image%2020201216144704.png)

- 安裝網站
辛苦那麼久，終於開始安裝囉！
![](img/Pasted%20image%2020201216144735.png)

- 設定網站
填上網站的相關設定
![](img/Pasted%20image%2020201216145353.png)

- 完成囉！ 
![](img/Pasted%20image%2020201216145444.png)

## 故障排除
看起來好好的網站，為什麼選單都不能用？！
![](img/Pasted%20image%2020201216145605.png) ![](img/Pasted%20image%2020201216145617.png)
> 出現 404 這類的錯誤時，可以去檢查一下 nginx 設定裡 error_log, access_log 設在什麼路徑，再去查查 log 檔寫了什麼

因為 Drupal 下面的各頁面雖然看起來是要連到網站下的路徑
![](img/Pasted%20image%2020201216160609.png)
但其實我們的實體結構並沒有這些目錄
![](img/Pasted%20image%2020201216160932.png)
實際上是讓 Drupal 的 index.php 去做查詢之後，把相關的結果重組成頁面再顯示出來

所以我們可以讓 nginx 來幫我們完成上面這些步驟
在 `/etc/nginx/conf.d/www.conf` 加入下圖區塊部份的程式碼
![](img/Pasted%20image%2020201216160432.png)
重置 Nginx 設定檔
`nginx -s reload`
都正常囉！
![](img/Pasted%20image%2020201216161359.png)
