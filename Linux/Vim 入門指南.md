# 鍵盤指令
i - insert
a - append
o - open
c - change
s - substitute
y - yank
d - delete
p - paste
r - replace
u - undo

# 分頁與導航
## 開啟其他檔案指令
`:e {filename}`
`:e .` - 開啟目錄檔案清單
## 分頁指令
`:tabe {filename}`
`:tabe .`
## 切換分頁指令
上一頁 - `g t`
下一頁 - `g T`
## 關閉分頁指令
`close`, `clos`

# 光標移動與定位
左 - j
下 - j
上 - k
右 - l
## 執行指令次數
{數字} + {指令}
## 定位指令
頁面底端 - G
頁面頂端 - gg
## 翻頁指令
向下半頁 - control + d (down)
向上半頁 - control + u (up)
向下整頁 - control + f (forward)
向上整頁 - control + b (back)
向下一行 - control + e
向上一行 - control + y
## 光標定位指令
置中 - z z
行首 - z t
行尾 - z b
畫面頂行 - H
畫面尾行 - L
## 跳轉指令
`: {行數}` - 普通模式
`{行數} G` - 錄製巨集
## 查找指令
`/ {查找文字}`
下一個 - n
上一個 - N
## 水平移動指令
移動到最後 - $
移動到最後 - 0
移動到文字最後（忽略空格符號） - ^
移動到文字最後（忽略空格符號） - g_
單字開頭 - w
單字結尾 - e
上一個單字開頭 - b
單字開頭（以空格符號為基準） - W
單字結尾（以空格符號為基準） - E
上一個單字開頭 - B
## 字符查找指令
移動到字符 - f (find)
移動到字符前一格 - t (to)
反向移動到字符 - F
反向移動到字符後一格 - T
重覆上一次的查找指令 - ;

# 插入指令模式
## 進入插入模式指令
光標位置 - i (insert)
光標後方 - a (append)
光標下方 - o (open)
取代字符 - s (substitute)
取代文字 - c (change) 需要再搭配一個光標移動指令
替換一個單詞 - c w
替換到結尾的文字 - c $
替換到第一個引號 - c f "
當前的行首 - I
當前的行尾 - A
當前的上一行 - O
取代整行文字 - S
取代到行尾 - C （等同於 c$ ）
## 退出插入模式
Esc
control + \[

# 普通模式指令
## 普通模式的編輯指令
刪除字元 - x
刪除前一個字元 - X
（ x - 普通模式的退格鍵）
取代字元 - r (replace) 後方接要取代的字元
取代模式 - R 完成後 Esc 退出
刪除單行文字 - d d
拷貝單行文字 - y y
貼上文字 - p / P
縮排 - > >
反向縮排 - < <
重複上一次的指令動作 - .
恢復 - u (undo)
重做 - control + r (redo)
一次性恢復單行指令 - U
## 數字遞增與遞減
遞增 - control + a
遞減 - control + x

# 參考資料
- https://www.youtube.com/watch?v=Yk4s-WLjxug&list=WL&index=9
- 書籍：vim 實用技巧