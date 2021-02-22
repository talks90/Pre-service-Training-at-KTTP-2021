### birdge
參數 - `-net=bridge`

是 Docker Compose 預設的網路模式，所有開啟容器的網路都會是 bridge (見 [network_mode](https://docs.docker.com/compose/compose-file/compose-file-v2/#network_mode) )

當 Docker Compose 啟動時，會建立一個名為 <font color=red>dirName_default</font> 的網段，並把 Docker Compose 內的所有容器都接上 <font color=red>dirName_default</font> 這個網段 ![](img/Pasted%20image%2020201217015447.png)

![](img/20110371THr1CEnIA0.png)
> <font color=red>172.28.0.0/16</font> 是指一網路的網段從 <font color=red>172.28.0.1 ~ 172.28.255.255</font>，子網路的主機可以互相通訊
	
####  Container <-> Container
Container 間如何通訊：

1. 直接寫 serivce name/container
	```
	backend:
        …略
        environment:
            PORT: 3001
            NODE_ENV: "development"
            MONGODB_URL: "mongodb://database:27017"
	```
	<font color=red>backend</font> 可以訪問 <font color=red>database</font>
2. 也可以直接指定 IP
	指令 `docker network inspect network_name` 可以查詢「接上此 network 的 container 資訊」

#### 外界(NET-0) -> Container
外界(NET-0)如何存取 container：

舉例來說，如果要讓外界 IP <font color=gray>192.168.0.5</font> 可以訪問我們的主機 80 通訊埠 的 <font color=red>ithelp.frontend</font> 服務時，在我們要啟動容器時一併設定 <font color=red>-p/--publish</font> 的參數；或是在 Docker file 中加上 <font color=red>ports:</font> 的設定

同時， [Network Address Translation(NAT)](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E5%9C%B0%E5%9D%80%E8%BD%AC%E6%8D%A2) 會自動設定內外部 network 的轉換
![](img/20110371rASyDVgmYE.png)
外界不會發現內部的網路
![](img/201103712FhvGKYHKI.png)

#### Container -> 外界(NET-0)/HOST
存取 host/外界 ：

container 要與外界通訊，除非有特殊需求要去調整 dns/host 之類的設定外，不然只需要跟平常一樣用 hostname / ip 就可以了

反而，當有要用 Container 存取 HOST 上的 MongoDB / MySQL 之類的服務時，需要考慮下列兩種情況：

- 非固定 IP 情況
container 在建立時就會完成網路的設定，如果沒有重新建立新的容器的話 IP 是不會改變的。
在 bridge 模式下，可以直接把 NAT 區塊視為是 HOST；因此，在容器中要訪問 HOST 的話，是連到 <font color=red>DOCKER0 的 gateway IP</font>，而不是 <font color=red>127.0.0.1</font> (127.0.0.1 是指容器本身)
> 可以登入 container (`docker run -it <id/container_name> bash`) 試打看看(ex: `telenet/ping/wget/curl`)，就可以知道有沒有通

- 固定 IP 情況
有時候我們會需要容器是固定 IP 的網路環境；例如 Nginx 要導轉到 HOST 的舊網址、Nginx 要導轉到某個 Container 或 MonoDB
這時我們就無法接受每次重建新容器時網路環璄就會改變；因此，可以用自定網路的方式來設定固定 IP，不但固定容器 IP，又可以固定 HOST IP
> 自定網路的原理和 bridge 相同，唯一不同的是它有 DNS 的功能，在同一網路間的容器可以相通

為了固定 IP，我們需要另外建立一個虛擬網路 <font color=gray>(ithelp_application)</font> 來設定裡面的 <font color=red>subnet: 和 gateway:</font>
接著，每個容器在建立時都指定用 <font color=gray>ithelp_application</font> 這個虛擬網路，並指定固定 IP

YAML 檔的設定如下：
```
version: '2'
services:
    database:
        image: mongo:4.1
        container_name: mongo4
        networks:
            ithelp_application:
                ipv4_address: 172.28.0.2
        ports:
            - "27017:27017"
        volumes:
            - "./data/mongo/data:/data/db"
    frontend:
        image: ithelp/frontend:1.0.0
        container_name: ithelp.frontend
        networks:
            ithelp_application:
                ipv4_address: 172.28.0.3
        ports:
            - "80:80"
        volumes:
            - "./data/nginx/log:/var/log/nginx"
    backend:
        image: ithelp/backend:1.0.0
        container_name: ithelp.backend
        networks:
            ithelp_application:
                ipv4_address: 172.28.0.4
        ports:
            - "3001:3001"
        command: >
            /bin/bash -c "
            sleep 15;
            npm run start;"
        environment:
            PORT: 3001
            NODE_ENV: "development"
            MONGODB_URL: "mongodb://database:27017"
networks:
    ithelp_application:
        driver: bridge
        ipam:
            driver: default
            config:
                - subnet: 172.28.0.0/16
                  gateway: 172.28.0.1
```

完成後就可以下 `docker-compose -f docker-compose-bridge-static.yml up` 指令來把整個環境架置起來；並用 `docker network inspect ithelp-30dayfullstack-day30_ithelp_application` 指令來看看我們固定的 IP

### host
參數 - `-net=host`
直接共用主機的網路 - 除了網路之外，其他的部分還是隔離獨立的
> 目前這個模式僅在 Linux 上有效，無法在 Mac or Windows 上使用

這種模式相對簡單，可以直接把容器當成是在 HOST 上執行的一個程式，容器使用哪一個 port ，主機的哪一個 port 就被占用；也不需要下 `-p / --publish` 參數

每個容器都用 <font color=red>127.0.0.1:port</font> 來通訊，跟主機上的其它的服務沒兩樣；而對外的 IP 就是用 <font color=red>本機對外網卡 ( eth0 ) 的 IP 位址</font>
![](img/201103712yVHsEAsOQ.png)

> 共用內容包含了 網卡，路由表，防火牆 … 等

### container
參數 - `-net=container:Name/ID`
使用指定容器的網路 - 除了網路之外，其他的部分還是隔離獨立的


---

主機端 ![](img/Pasted%20image%2020201210091443.png)
容器端 ![](img/Pasted%20image%2020201210091136.png)