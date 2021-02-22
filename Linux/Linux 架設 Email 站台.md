# E-mail 收發過程
![](img/76110.jpg)
1. 當寄出一封 e-mail 時，會走 SMTP (25 port) 送給 mail server
2. mail server 先跟 DNS 查收件人 mail server 的 IP，再把 mail 傳送過去
3. 收件人mail server 收到 mail 之後，會先存到 tmp 資料庫內管理
4. 收件人要收件時，會以 POP3 (110 port) 連線到收件人的 mail server 下載信件
> 舊的協定是 IMAP (143 port)

參考網站 [Server World](https://www.server-world.info/en/note?os=CentOS_8&p=mail&f=1)
# 安裝 Postfix 
Postfix 是用來架 SMTP 郵件伺服器的軟體
`dnf -y install postfix` ![](img/Pasted%20image%2020201217161501.png)
## 設定檔
這裡會用到的設定檔總共有 2 個
### /var/named/student.zone
先把 mail server 加入網域 ![](img/Pasted%20image%2020201217164451.png)
重新啟動 DNS 服務 `systemctl restart named`
查詢 DNS 能不能順利查到 mail server
`dig -t MX student15.example.tw` ![](img/Pasted%20image%2020201217165312.png)

### /etc/postfix/main.cf
==第 94 行：取消註解，並設定伺服器名稱==
`myhostname = mail.student15.example.tw`

 ==第 102 行：取消註解，並設定網域名稱==
`mydomain = student15.example.tw`

==第 118 行：取消註解==
`myorigin = $mydomain`

==第 132 行：取消註解、第 135 行：加註解==
`inet_interfaces = all`

==第 138 行：改為只用 IPv4==
`inet_protocols = ipv4`

==第 183 行：行末加上, $mydomain==
`mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain`

==第 268 行：取消註解==
`mynetworks_style = class`

==第 283 行：取消註解，並設定為伺服器 IP==
`mynetworks = 192.168.43.131/24, 127.0.0.0/8`

==第 438 行：取消註解==
`home_mailbox = Maildir/`

==第 593 行：取消註解，並將句末 $mail_name 刪除==
`smtpd_banner = $myhostname ESMTP`

==加到設定檔最後面==
==一封 email 的上限容量為 10M，可加可不加==
`message_size_limit = 10485760`

==信箱容量的上限為 1G，可加可不加==
`mailbox_size_limit = 1073741824`

==SMTP-Auth 的設定值==
```
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_local_domain = $myhostname
smtpd_recipient_restrictions = permit_mynetworks, permit_auth_destination, permit_sasl_authenticated, reject
```

## 啟動 postfix 服務 
`systemctl enable --now postfix `

## 打開防火牆
`firewall-cmd --add-service=smtp --permanent`
`firewall-cmd --reload `

# 安裝 Dovecot 
Dovecot 是用來架郵件存儲服務器的軟體
`dnf -y install dovecot` ![](img/Pasted%20image%2020201221141035.png)
## 設定檔
這裡會用到的設定檔總共有 5 個
### /etc/dovecot/dovecot.conf
==第 30 行：取消註解（如果沒有用 IPv6 可以把 , :: 刪除）==
`listen = *`

### /etc/dovecot/conf.d/10-auth.conf 
 ==第 10 行：取消註解，並把設定值改為 no（可以用 plain text 認證方式）==
`disable_plaintext_auth = no`
==第 100 行：在行末加上 login ==
`auth_mechanisms = plain login`

### /etc/dovecot/conf.d/10-mail.conf
==第 30 行：取消註解，並加上設定值 maildir:~/Maildir ==
`mail_location = maildir:~/Maildir`

### /etc/dovecot/conf.d/10-master.conf 
==第 107-109 行：取消註解， 這個段落是設定 Postfix smtp-auth，將擁有者及擁有群組都設為 postfix ==

```
unix_listener /var/spool/postfix/private/auth {
  mode = 0666
  user = postfix
  group = postfix
}
```

### /etc/dovecot/conf.d/10-ssl.conf
 ==第 8 行：改為 yes（不強制使用 SSL）==
`ssl = yes`

## 啟動 dovecot 服務 
`systemctl enable --now dovecot `

## 打開防火牆
`firewall-cmd --add-service={pop3,imap} --permanent`
`firewall-cmd --reload `


# 安裝  Mailx
Mailx 是用來收發郵件的軟體
`dnf -y install mailx` ![](img/Pasted%20image%2020201221144926.png)

==設定使用 Mailx 的環境變數==
`echo 'export MAIL=$HOME/Maildir' >> /etc/profile.d/mail.sh`

## 寄信
![](img/Pasted%20image%2020201221150311.png)

## 收信
![](img/Pasted%20image%2020201221150553.png) ![](img/Pasted%20image%2020201221150734.png)