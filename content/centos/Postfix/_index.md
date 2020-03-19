+++
title = "Postfix"
weight = 18
+++


## Postfix

{{% notice note %}}
What is Postfix  
Wietse Venema의 메일 서버는 널리 사용되는 Sendmail 프로그램의 대안으로 IBM 연구에서 시작된 Mail Server 입니다.  
[Postfix Official Site  ](http://www.postfix.org/) 에서 자세한 정보를 확인할수 있습니다.  
{{% /notice %}}

{{% notice tip %}}
Mail Server 구축시 참고사항  
대략의 스팸메일 때문에 메일 서버를 구축하여도 포털 메일에서 확인이 안될수 있습니다.  
메일 서버 운영시 반드시 화이트 리스트에 등록을 해야 합니다.  문서내용은 테스트 용으로만 사용하시길 바랍니다. 
[kisarbl.or.kr](https://www.kisarbl.or.kr/whiteip/whiteip_tutorial.jsp) 에서 등록할수 있습니다.  
{{% /notice %}}

{{% notice info %}}
Centos7 or RHEL7  
Centos7 의 경우 Default 로 postfix 가 설치 되어 구동 되고 있습니다.  
{{% /notice %}}



* hostname 설정  

```no-highlight
root@centos7 ~]# hostnamectl set-hostname mail.test.com
```

* Postfix 설치  
```no-highlight
[root@mail ~]# yum install -y postfix
```

* Postfix 실행 및 Daemon 활성화  
```no-highlight
[root@mail ~]# systemctl start postfix
[root@mail ~]# systemctl enable postfix
```

* Postfix 설정  
```no-highlight
[root@cmail ~]# vi /etc/postfix/main.cf
myhostname = mail.test.com
mydomain = test.com
myorigin = $mydomain
inet_interfaces = all
#inet_interfaces = $myhostname
#inet_interfaces = $myhostname, localhost
#inet_interfaces = localhost
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

#relay_domains = $mydestination
```

* **main.cf 파일 설정값**    

|설정값    	|설정값     	|Default 설정     	|
|---	|---	|---	|
|queue_directory   	|mail Queue 디렉토리    	|/var/spool/postfix   	|
|command_directory   	|postfix command 디렉토리    	|/usr/sbin   	|
|daemon_directory   	|postfix daemon 디렉토리    	|/usr/libexec/postfix   	|
|data_directory   	|postfix data 디렉토리    	|/var/lib/postfix   	|
|mail_owner   	|postfix owner 설정     	|postfix   	|
|myhostname   	|hostname 설정   	|#myhostname = host.domain.tld   	|
|mydomain    	|domain 설정    	|#mydomain = domain.tld   	|
|myorigin   	|myorigin = $mydomain    	|#myorigin = $myhostname   	|
|inet_interfaces   	|메일을 수신에 사용할 네트워크 인터페이스를 지정   	|#inet_interfaces = all   	|
|inet_protocols   	|Default 값 사용    	|inet_protocols = all   	|
|proxy_interfaces   	|proxy 인터페이스 사용시 설정    	|#proxy_interfaces =   	|
|mydestination    	|메일 서버 수신 설정    	|mydestination = $myhostname, localhost.$mydomain, localhost   	|
|relay_domains   	|외부로 Relay 시킬경우 사용    	|#relay_domains = $mydestination   	|
|home_mailbox   	|사용자 홈디렉토리에 저장시 사용    	|#home_mailbox = Maildir/   	|
|mail_spool_directory   	|mail spool 디렉토리 설정    	|#mail_spool_directory = /var/spool/mail   	|
|smtpd_banner    	|클라이언트가 메일 서버 접속시 보여줄 Server 이름    	|#smtpd_banner = $myhostname ESMTP $mail_name   	|


* postfix Daemon 재시작  
```no-highlight
[root@mail ~]# systemctl restart postfix
```

* 방화벽 추가  
```no-highlight
[root@mail ~]# firewall-cmd --add-service=smtp --permanent
[root@mail ~]# firewall-cmd --reload
```

* Local mail test  
```no-highlight
[root@mail ~]# yum install mutt
[root@mail ~]# su - test
Last login: Fri Oct 26 21:10:47 KST 2018 from 192.168.0.1 on pts/0
[test@mail ~]$ mutt
/home/test1/Mail does not exist. Create it? ([yes]/no): no 
no 를 눌러 별도로 home mail 디렉토리를 생성하지 않습니다.  

m 을 눌러 메일을 작성 합니다. 
To: test1
Subject: mail test
mail server test

1
2
3
4
5
```
* y 를 눌러 메일을 전송 합니다.
<br></br>
![S11](/centos/centos_tmp/setting/S11.png)

<br></br>

* mail 확인  
> test1 user 로 유저를 변경후 mutt 로 메일을 확인 합니다.  
```no-highlight
[test@mail ~]$ su - test1
Password:
Last login: Sat Oct 27 07:34:55 KST 2018 on pts/0
[test1@mail ~]$ mutt
```

* mutt 확인시 test@mail.test.com 에서 온 메일을 확인할수 있습니다.  
![S12](/centos/centos_tmp/setting/S12.png)
<br></br>

* 해당 라인에서 엔터를 쳐서 메일 내용을 확인 합니다.  
![S13](/centos/centos_tmp/setting/S13.png)
<br></br>

{{% notice note %}}
muttrc 설정  
mutt 사용시 /home/User/Mail does not exist. Create it? ([yes]/no): 의 경우 .muttrc 가 설정 되어 있지 않아 나오는 메시지 입니다.  
{{% /notice %}}

* .muttrc 파일 생성  
```no-highlight
[test1@mail ~]$ vi .muttrc
set folder=/var/spool/mail
set mbox=+daedalus
```

* postfix 메일 큐 명령어  
```no-highlight
# 메일 큐 비우기  
[root@rhel7 ~]# postfix -f  

# 메일 큐에 쌓인 메일 확인  
[root@rhel7 ~]# mailq  

# 메일큐 전체 삭제  
[root@rhel7 ~]# postsuper -d ALL
```




{{% notice note %}}
mail server test  
2대의 vm 을 이용하여  mail server 를 구성 합니다.  
서로 다른 서버에서 mail 을 보내서 정상적으로 메일이 발신/수신 되는지 확인 합니다.  
별도의 dns 서버 셋팅이 필요 합니다.  
{{% /notice %}}

|hostname   |ip    |mail user   |
|---|---|---|
|mail.test.com   |192.168.0.20   |test01   |
|mail.test1.com   |192.168.0.30   |user01   |

* bind 설정  
[bind 설치](http://112.187.207.248/centos/centos/#dns) 에서 참고 하시면 됩니다.  

```no-highlight
dns 구성은 named-chroot 구성 입니다.  
설정디렉토리는 다음과 같습니다.  
[root@ns ~]# cd /var/named/chroot/var/named/
[root@ns etc]# vi named.rfc1912.zones



zone "test1.com" In {
        type master;
        file "test1.com";
        allow-update { none; };
};


[root@ns etc]# cd /var/named/chroot/var/named/
[root@ns named]# cp test.com test1.com

test.com 설정 
[root@ns named]# cat test.com
$TTL 3H
@       IN SOA  @ ns.test.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.test.com.
        IN      MX 10   mail.test.com.
        IN      A       192.168.0.20
mail    IN      A       192.168.0.20
ns      IN      A       192.168.0.20
www     IN      A       192.168.0.20


[root@ns named]# vi test1.com

$TTL 3H
@       IN SOA  @ ns.test1.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.test1.com.
        IN      MX 10   mail.test1.com.
        IN      A       192.168.0.30
mail    IN      A       192.168.0.30
ns      IN      A       192.168.0.30
www     IN      A       192.168.0.30




[root@ns named]# chown root:named test1.com

[root@ns named]# systemctl restart named-chroot
[root@ns named]# nslookup
> server
Default server: 192.168.0.10
Address: 192.168.0.10#53
> mail.test.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   mail.test.com
Address: 192.168.0.20
> mail.test1.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   mail.test1.com
Address: 192.168.0.30
>
[root@ns named]#
```



* resolv.conf 를 변경 합니다.  
> mail.test.com / mail.test1.com 모두 변경 합니다.  
```no-highlight
[root@mail ~]# vi /etc/resolv.conf
# Generated by NetworkManager
#nameserver 8.8.8.8
nameserver 192.168.0.10
```

* test.com -> test1.com 의 user01 로 메일을 보냅니다.  
```no-highlight
[root@mail ~]# su - user01
[test01@mail ~]$ mutt
To: user01@mail.test1.com
Subject: mail.test.com server test

test.com mail Server test

1
2
3
4
5

    From: test01@mail.test.com
      To: user01@mail.test1.com
      Cc:
     Bcc:
 Subject: test.com mail Server test
Reply-To:
     Fcc: ~/sent
Security: None

y를 눌러 메일을 전송 합니다. 
```

* mail.test1.com 에서 메일을 확인 합니다.  
```no-highlight
[root@mail ~]# su - user01
Last login: Sun Oct 28 08:18:51 KST 2018 on pts/0
[user01@mail ~]$ mutt
```
* mutt 로 확인시 메일이 한통 도착한걸 확인 할수 있습니다.  

![S14](/centos/centos_tmp/setting/S14.png)

* mail.test.com 에서 온 메일을 확인 할수 있습니다.  

![S15](/centos/centos_tmp/setting/S15.png)

* mail.test1.com -> mail.test.com 으로 메일을 전송해 봅니다.  

{{% notice tip %}}
Web mail 운영시 참고사항  
[dovecot](https://www.dovecot.org/) , [roundcube](https://roundcube.net/) 를 이용하여 web mail 을 구성할수 있습니다.  
mail Server 에서는 자체 web-mail GUI 환경을 제공 하지 않습니다. gui 부분은 roundcube 로 사용을 하시면 편하게 web에서 메일을 볼수 있습니다.  
dovcot 의 경우 Unix/Linux 환경에서 사용할수 있는 IMAP / POP3 전자메일 서버 입니다.  
{{% /notice %}}



<br></br>