# Step 1 - 安裝並開啟模組
- Facet
![](img/Pasted%20image%2020210208214222.png)
- Search API
![](img/Pasted%20image%2020210208214305.png)

# Step 2 - 新增內容類型
新增搜尋時要用的內容類型，也可以用 Drupal 內建的類型
![](img/Pasted%20image%2020210208214927.png)
![](img/Pasted%20image%2020210208215016.png)

# Step 3 - 設定搜尋伺服器
先到設定頁面 **設定 -> 搜尋與 Metadata -> Search API**
![](img/Pasted%20image%2020210208215405.png)
新增搜尋伺服器及設定名稱
![](img/Pasted%20image%2020210208215916.png)
![](img/Pasted%20image%2020210208220128.png)
![](img/Pasted%20image%2020210208220204.png)

# Step 4 - 設定搜尋索引
新增搜尋索引及設定名稱，並設定要用來搜尋的項目
![](img/Pasted%20image%2020210208221719.png)
選擇搜尋的範圍
![](img/Pasted%20image%2020210208221431.png)
選擇搜尋伺服器
![](img/Pasted%20image%2020210208222059.png)
設定索引方式
![](img/Pasted%20image%2020210208222138.png)

# Step 5 - 設定索引的欄位
![](img/Pasted%20image%2020210208222355.png)
設定需要的欄位
![](img/Pasted%20image%2020210208223120.png)

# Step 6 - 建立索引
按下 **Index Now** 後開始建立索引
![](img/Pasted%20image%2020210208223805.png)

# Step 7 - 新增 Views 頁面
先到設定頁面 **架構 ->Views**
![](img/Pasted%20image%2020210208224201.png)
Add Views
![](img/Pasted%20image%2020210208224301.png)
![](img/Pasted%20image%2020210208225048.png)
![](img/Pasted%20image%2020210208224704.png)

# Step 8 - 在 Views 頁面新增索引的欄位
![](img/Pasted%20image%2020210208225554.png)
選擇前面所設定的索引欄位
![](img/Pasted%20image%2020210208225833.png)
![](img/Pasted%20image%2020210208230240.png)

# Step 9 - 設定 Facets
![](img/Pasted%20image%2020210208230648.png)
新增 Facets
![](img/Pasted%20image%2020210208230738.png)
設定項目
![](img/Pasted%20image%2020210208230917.png)
選擇清單形式
![](img/Pasted%20image%2020210208231513.png)
不要顯示機器可讀名稱
![](img/Pasted%20image%2020210208231713.png)

# Step 10 - 讓 Facets 區塊在頁面中顯示
先到設定頁面 **架構 ->區塊的頁面布局**
![](img/Pasted%20image%2020210208232308.png)
選擇要放置的區域
![](img/Pasted%20image%2020210208233424.png)
![](img/Pasted%20image%2020210208233113.png)


# 源 - 速記
完成的成果會在新增的 view 頁面中顯示
先調出要搜尋的功能，再來調整頁面的版面

Bootstrap 4 可以支援 if 的功能
有內容才顯示，沒有內容就不顯示
調整過 bootstrap 的版型後，要 drush cr(cach rebuild; before drupal 7, use drush cc: cach clear)

設定 col 時，第 1 個 col 指定寬度，第 2 個不指定可以保持彈性
當第 1 個 col 沒有內容時，就可以滿版，有內容時就調整成剩下的寬度

drupal 8 有獨立出一個設定檔，讓 vender 資料夾肥大
在 stage 階段用 yaml 檔來管理
設定在/var/www/sites/7starlake.com/web/themes/custom/sevenstarlake/templates/page 下的 page.html.twig 檔
有 region 的概念

# 工作進度
## 1100218
- 將 products search views 頁面的顯示欄位依範例排好
- 測試不同格式的顯示方式，目前使用表格，試了 Blazy Grid、HTML 清單、Views Layout Grid 都不是想要的樣式
- 調整 Facets 項目的順序，改成 checkbox 的型式；但只有出來 CPU 的選項，而且沒有排序