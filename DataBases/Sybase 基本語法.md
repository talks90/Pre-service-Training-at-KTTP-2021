# 訪問 SQL SVR
`isql -U 用戶名 -P 密碼 -S 服務名 -J 字符集名`
服務名 - 客戶端配置文件名稱 ( linux 一般為 interface，windows 一般為 sql.ini)
字符集 - sybase 如果是 utf8，使用 isql 連接時用 cp936 可正確使用中文

# 啟動 sybase
`%$SYBASE/ASE-12_5/install/startsever -f RUN_服務名`
`%$SYBASE/ASE-12_5/install/startsever -f RUN_服務名_BACKUP`

# 關閉 sybase
用 isql 登錄 sybase、查詢 sysservers 表，查看可關閉服務
```
isql -Usa -Sjoli
Password:
1> select * from syservers
2> go
```
一般需關閉的服務有：系統備份服務、系統主服務，在 isql 裡先關備份服務（默認 SYB_BACKUP），再關主服務（默認可不輸入）
```
1> shutdown SYB_BACKUP
2> go
1> shutdown
2> go
```

# sybase 用戶管理
## 新增登錄用戶
`sp_addlogin login_name, passwd[,defaultdb[,def language[,fullname]]]`
## 刪除登錄用戶
`sp_druplogin login_name`
## 新增 user（資料庫用戶）
`sp_adduser login_name[,name_in_db[,grpname]]`
## 刪除 user
`sp_drupuser login_name`
## 修改該 user 為該資料庫 dbo
```
1> use joli_db
2> sp_changedbowner joli
```

# 查找資料庫用戶信息
`sp_displaylogin [login_name]`
`sp_helpuser`
`sp_who`
## 修改密碼
`sp_password old_password, new_password`
sa, old_password 可為 null

# 新增設備
```
1> disk init
2> name = "joli_dev"
3> physname = "/opt/sybase/joli/joli_dev.dat"
4> vdevno=9 (設備編號，不能重覆)
5> size=51200 (100M，單位為 2k)
6> go
（一般為用戶數據庫創建一個數字設備後，再單獨為用戶數據庫創建一個日誌設備）
```

# 刪除設備
需要確定該設備上沒有資料庫
```
1> sp_dropdevice test_dev
2> go
```
再刪除操作系統設備文件

# 查看設備狀態
```
1> sp_helpdevice [設備名]
2> go
```
可查看設備是否鏡像（相關鏡象類型、是否有臨時取消鏡象等）配置

# 創建資料庫
```
1> create database joli_db on joli_dev=50 log on joli_log_dev=20
2> go
```
創建用戶資料庫 joli_db ，該資料庫數據儲存在設備 joli_dev 設備上，可使用大小為 50 M ；日誌儲存在 joli_log_dev 設備上，可使用大小為 20 M

# 擴展資料庫
`alter database db_name on device_name=size log on device_name=size`
```
1> alter database joli_db on joli_dev=10 log on joli_log_dev=5
2> go
```

# 刪除資料庫
`drop database db_name`
# 檢查資料庫狀態
## 一般性檢查
```
1> sp_helpdb
2> go
```

## 系統性檢查
所有資料庫
```
1> dbcc checkdb
2> go
```

指定用戶資料庫
```
1> dbcc checkalloc(joli_db)
2> go
```

# Sybase 日誌管理
創建用戶資料庫時，應盡量為事務日誌創建獨立的日誌設備（一般為資料庫的 20％），這樣可以單獨備份事務資料，防止資庫滿溢，可以看到事務日誌的占用情形及可以鏡象

`dump transaction db_name with truncate_only`
// 不備份 log，直接清除
`dump transaction db_name with no log`
// 備份 log
`dump transaction db_name to "path/name"`
檢查 log 大小
```
1> dbcc check table (syslogs)
2> go
```
快速查看 log 大小
```
1> select data_pgs (8, doa mpg)
2> from sysindexes where id=8
3> go
```

# 修改 Sybase 系統默認值
修改 $SYBASE/ASE-12_5 目錄 “服務名.CFG” 文件，重新啟動 sybase 服務來修改 sybase 系統參數

查詢當前所有參數配置
```
1> sp_configure
2> go
```
查詢可動態修改的參數
```
1> sp_configure "abcdef"
2> go
```
查詢特定參數的配置情形
```
1> sp_configure "參數名"
2> go
```
一般可視狀況修改以下參數：
cache size
number of open databases
number of open objects
number of open indexes
partition groups
number of large i/o buffers
number of devices
default network packet size
max network packet size
number of remote connections
number of remote logins
number of network processes

# 檢查資料庫版本
```
1> select @@ version
2> go
```

# 備份到磁碟
## 本地備份
```
1> dump database joli_db to "絕對路徑/name"
2> go
```
## 遠端備份
`dump database_name to "/u01/data/0102.dmp" at remote_server`
`dump transaction database_name to "/u01/log/0102.dmp" at remote_server`

# 恢復資料庫
`v load database 資料庫名 from "dev/rmt/0"`

# Sybase 資料庫修改表的列名
`sp_rename '表名.列名'; '新的列名', 'column'`

# 查看 Sybase 資料庫內的所有列表
`select name from sysobjects where type="U"`

# 自增 ID 相關操作
```
create table test_table (id numberic(18, 0) identity, strl varchar(20) not null) look all pages with identity_gap=1 on 'default'
go
set identity_insert test_table on
go
insert into test_table (id, strl) values (1000, 'bbb')
go
set identity_insert test_table off
go
set identity_insert test_table on
go
update test_table set id=id+1000 where id<1000
go
set identity_update test_table off
go
insert into test_table(strl) values('ccc')
關鍵字用 [] 括起來
```