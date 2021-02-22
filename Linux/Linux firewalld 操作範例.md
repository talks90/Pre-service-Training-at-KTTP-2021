範例：

例一. 將原本預設的public zone改成使用dmz zone

1. 拷貝一份dmz檔
    `# cp /usr/lib/firewalld/zones/dmz.xml /etc/firewalld/zones/`

2. 修改firewalld.conf檔
    ```
	# vi /etc/firewalld/firewalld.conf
    DefaultZone=dmz
	```
    備註：
    A. dmz 是預設使用的zone，可以改成要用的
    B. 設定檔的格式說明，可以使用指令「man firewalld.conf」查詢

3. 重新載入
    `# firewall-cmd --complete-reload`

例二. 將原本防火牆預設的SSH的通訊埠(22)改至23456

1. 拷貝一份ssh.xml檔
    ```
	# cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/
	```
	
	

    修改/etc/firewalld/services/ssh.xml檔
    # vi /etc/firewalld/services/ssh.xml
    <service>
           <short>SSH</short>
           <description>（說明文字省略）</description>
           <port protocol="tcp" port="23456"/>
    </service>
    備註：
    A. 只須將紅色字部分，修改成你需要的通訊埠即可
    B. 設定檔的格式說明，可以使用指令「man firewalld.service」查詢

    修改/etc/firewalld/zones/dmz.xml檔（以使用dmz zone為例）
    # vi /etc/firewalld/zones/dmz.xml
    <?xml version="1.0" encoding="utf-8"?>
    <zone>
           <short>DMZ</short>
           <description>（說明文字省略）</description>
           <service name="ssh"/>
    </zone>
    備註：
    A. 若/etc/firewalld/zones目錄裡無該檔案，可以從/usr/lib/firewalld/zones目錄中拷貝一份
    B. service name就是檔案名稱
    C. 這樣的設法，表示SSH接受任何IP的連線
    D. <service>可以多個
    E. 設定檔的格式說明，可以使用指令「man firewalld.zone」查詢

    重新載入
    # firewall-cmd --complete-reload

 

例三. 限制SSH只能由特定的IP連線

    修改/etc/firewalld/zones/dmz.xml檔（以使用dmz zone為例）
    # vi /etc/firewalld/zones/dmz.xml
    <?xml version="1.0" encoding="utf-8"?>
    <zone>
           <short>DMZ</short>
           <description>（文字說明省略）</description>
           <rule family="ipv4">
                  <source address="211.72.188.188"/>
                  <service name="ssh"/>
                  <!-- <log prefix="sshlog" level="info"/> -->
                  <accept/>
           </rule>
    </zone>
    備註：
    A. 無須考慮ICMP封包的問題，除非有特別想阻擋的ICMP項目
    B. 檔案裡，要註解某一行時，請用<!--   -->
    C. <service>寫在<rule>裡的，只能有一個<service>
    D. 將source address換成你允許開放連線的IP
    E. 上面的例子<log>是無作用，若要記錄此規則的相關連線訊息，可以將<!--  -->刪除，它會將資料記錄在/var/log/messages裡
    F. 若語法有錯誤，重新載入時，會將錯誤寫在/var/log/firewalld裡

    重新載入
    # firewall-cmd --complete-reload

 

例四. 加入一個新服務（例如：OpenVAS）

    新增一個/etc/firewalld/services/openvas.xml
    # vi /etc/firewalld/services/openvas.xml
    <?xml version="1.0" encoding="utf-8"?>
    <service>
           <short>OpenVAS Test</short>
           <description>（文字說明省略）</description>
           <port protocol="tcp" port="9392"/>
    </service>
    備註：
    A. 檔案名稱就是服務名稱
    B. <short>與<description>可自己取名與描述
    C. <port>填入該服務預使用的通訊埠

    修改/etc/firewalld/zones/dmz.xml檔（以使用dmz zone為例）
    # vi /etc/firewalld/zones/dmz.xml

    <?xml version="1.0" encoding="utf-8"?>
    <zone>
           <short>DMZ</short>
           <description>（說明文字省略）</description>
           <service name="ssh"/>
           <service name="openvas"/>
    </zone>
    備註：
    A. 多加入一個<service>，名稱就是檔案名稱
    B. 這樣預設會開放所有IP連線
    C. 若要限制只有特定IP連線，可以參考【例三】，多新增一個<rule>即可

    重新載入
    # firewall-cmd --complete-reload

 

例五. 使用iptable的語法來設定（以使用dmz zone為例）

    檢查/etc/firewalld/zones/dmz.xml檔（以使用dmz zone為例）
    # vi /etc/firewalld/zones/dmz.xml
    <?xml version="1.0" encoding="utf-8"?>
    <zone>
           <short>DMZ</short>
           <description>（說明文字省略）</description>
           <service name="ssh"/>
    </zone>
    備註：
    A. 刪除原本的<service>，全由direct設定的規則來控制即可。

    新增一個/etc/firewalld/direct.xml檔
    # vi /etc/firewalld/direct.xml
    <?xml version="1.0" encoding="utf-8"?>
    <direct>
           <rule priority="0" table="filter" ipv="ipv4" chain="INPUT">-p tcp -s 211.72.188.188/32 --dport 23456 -j ACCEPT</rule>
    </direct>
    備註：
    A. 設定的語法可以參考iptable
    B. 設定檔的格式說明，可以使用指令「man firewalld.direct」查詢

    重新載入
    # firewall-cmd --direct --remove-rules ipv4 filter INPUT
    # firewall-cmd --complete-reload
    備註：
    A. 目前測試發現firewall-cmd重新載入時，不會自動將先前的規則清除，只能手動先清除之前設定的規則後，再重新載入
    B. --remove-rules後面需要3個參數：<ipv> <table> <chain>

    查看目前的Direct規則
    # firewall-cmd --direct --get-all-rules