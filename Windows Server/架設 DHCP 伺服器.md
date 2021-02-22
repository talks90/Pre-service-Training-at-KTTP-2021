#Windows伺服器環境規劃建置 

DHCP - Dynamic Host Configuration Protocol

- 169.254.x.x/16 - 是透過 APIPA 取得的，表示 DHCP 的 IP 取得有問題

- 1/2, 7/8 法則

lease time : 租期；會取決於翻桌率，用戶來來去去就可以設短一點，用戶不太變動就可以設長一點

在租期的 1/2 時， DHCP SVR 會詢問 CLT 

# 題目 1 - DHCP + IIS

## IP 設定
- scope - 192.168.1XX.51 - 60/24
- DNS - 192.168.1XX.1
- SVR - 192.168.1XX.1
- CLT - 自動取得

## 架構

```
C
| --company
	|--web
		|--index.htm
```

## 要求 
- 在 CLT 端輸入 http://www2.test.com 可連到 index 網頁

## 安裝步驟
1. 安裝 DHCP 角色，其他都下一步 ![](../img/Pasted%20image%2020201111134844.png)![](../img/Pasted%20image%2020201111135103.png)
2. ![](../img/Pasted%20image%2020201111143156.png)
3. ![](../img/Pasted%20image%2020201111143248.png)
4. ![](../img/Pasted%20image%2020201111143333.png)
5. ![](../img/Pasted%20image%2020201111143519.png)
6. ![](../img/Pasted%20image%2020201111143743.png)
7. ![](../img/Pasted%20image%2020201111143843.png)
8. ![](../img/Pasted%20image%2020201111143920.png)
9. ![](../img/Pasted%20image%2020201111143956.png)
10. ![](../img/Pasted%20image%2020201111144113.png)
11. ![](../img/Pasted%20image%2020201111144433.png)![](../img/Pasted%20image%2020201111144514.png)
12. ![](../img/Pasted%20image%2020201111144707.png)
13. ![](../img/Pasted%20image%2020201111144741.png)
14. ![](../img/Pasted%20image%2020201111144813.png)
15. ![](../img/Pasted%20image%2020201111145029.png)
16. CLT 端取得 DHCP 派發的 IP 囉![](../img/Pasted%20image%2020201113101439.png)


# 題目 2 - DHCP 排除與保留

## 架構

```
------------------------
	|		|		|
  COM_A	  COM_B	  COM_C
DHCP_SVR   CLT1    CLT2
```

## IP 設定
- A - 192.168.1XX.1/24
- B - DHCP

## DHCP 設定
- scope - 192.168.1XX.70 - 75/24
- 排除 - 192.168.1XX.71 - 75
- 保留 - 192.168.1XX.70 -> CLT1 (B)

## 測試步驟
1. 將 B, C 網卡先停用
2. 單獨開啟 C 網卡，IP 為？
	1. 169.254.X.X/16
	2. 0.0.0.0
	3. 192.168.1XX.70/24
	4. IP 衝突
![](../img/Pasted%20image%2020201113152722.png)

3. 再將 B 網卡啟用，IP 為？
	1. 169.254.X.X/16
	2. 0.0.0.0
	3. 192.168.1XX.70/24
	4. IP 衝突
![](../img/Pasted%20image%2020201113153854.png)