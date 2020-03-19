+++
title = "DNS"
weight = 23
+++


## DNS  

{{% notice note %}}
bind infomation  
BIND는 DNS (Domain Name System) 정보를 인터넷에 게시하고 사용자의 DNS 쿼리를 해결할 수있는 오픈 소스 소프트웨어입니다.  
BIND라는 이름은 "버클리 인터넷 이름 도메인 (Berkeley Internet Name Domain)"의 약자로 1980 년 초 버클리 캘리포니아 대학에서 소프트웨어가 시작 되어서 붙여진 이름 입니다.  
{{% /notice %}}


{{% notice tip %}}
**web server 사용시 tip**  
Web Server Test 시 apache Virtual domain 테스트시 bind 와 같이 설정 하여 멀티 도메인 설정을 할수 있습니다.  
간편하게 Windows / Linux hosts 파일 변경 하여 사용하여도 됩니다.  
{{% /notice %}}

* bind install  

```no-highlight
[root@centos7 ~]# yum install bind-*
```

* named.conf 설정  
```no-highlight
[root@centos7 ~]# vi /etc/named.conf
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };

```


* 도메인 추가  
> rev 파일  
> 역방향 설정의 경우 test.com 만 설정 하였습니다. ip 주소로 도메인 검색시 역방향 설정을 합니다.  
> 필요에 따라 설정 하시면 됩니다.  


> test.com / a.com / b.com 을 만듭니다.  
> 주석 부분의 경우 역방향 조회 부분입니다.  
```no-highlight
[root@centos7 ~]# vi /etc/named.rfc1912.zones
zone "test.com" In {
        type master;
        file "test.com";
        allow-update { none; };
};
 
zone "0.168.192.in-addr.arpa" IN {
        type master;
        file "test.com.rev";
        allow-update { none; };
};
 
zone "a.com" In {
        type master;
        file "a.com";
        allow-update { none; };
};
 
#zone "0.168.191.in-addr.arpa" IN {
#       type master;
#       file "a.com.rev";
#       allow-update { none; };
#};
 
zone "b.com" In {
        type master;
        file "b.com";
        allow-update { none; };
};
```

* zone 파일 생성  

**DNS 레코드 참고**  

|레코드    |설명     |레코드    |설명    |
|---|---|---|---|
|SOA   |존의 권한시작    |NS   |권한을 가진 네임서버   |
|A   |호스트 주소    |CNAME   |Alias canonical 이름    |
|MX   |메일 익스체인저    |PTR   |도메인 네임 포인터 (역방향 DNS에 사용 )   |

```no-highlight
[root@centos7 ~]# cd /var/named/
[root@centos7 named]# cp named.empty test.com
[root@centos7 named]# cp named.empty a.com
[root@centos7 named]# cp named.empty b.com
[root@centos7 named]# vi test.com
$TTL 3H
@       IN SOA  @ ns.test.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.test.com.
        IN      MX 10   mail.test.com.
        IN      A       192.168.0.10
mail    IN      A       192.168.0.10
ns      IN      A       192.168.0.10
www     IN      A       192.168.0.10

[root@centos7 named]# vi a.com
$TTL 3H
@       IN SOA  @ ns.a.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.a.com.
        IN      MX 10   mail.a.com.
        IN      A       192.168.0.10
mail    IN      A       192.168.0.10
ns      IN      A       192.168.0.10
www     IN      A       192.168.0.10

[root@centos7 named]# vi b.com
$TTL 3H
@       IN SOA  @ ns.b.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.b.com.
        IN      MX 10   mail.b.com.
        IN      A       192.168.0.10
mail    IN      A       192.168.0.10
ns      IN      A       192.168.0.10
www     IN      A       192.168.0.10
```

* 역방향 설정  

```no-highlight
[root@centos7 named]# vi test.com.rev
$TTL 1D
@       IN SOA  @ ns.test.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.test.com.
10      IN      PTR     ns.test.com.
```

* 권한 변경  
```no-highlight
[root@centos7 named]# chown root:named a.com
[root@centos7 named]# chown root:named b.com
[root@centos7 named]# chown root:named test.com
[root@centos7 named]# chown root:named test.com.rev
```


* bind 실행 및 Daemon 활성화  
```no-highlight
[root@centos7 ~]# systemctl start named
[root@centos7 ~]# systemctl enable named
```

* 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --add-port=53/tcp --zone=public
[root@centos7 ~]# firewall-cmd --permanent --add-port=53/udp
[root@centos7 ~]# firewall-cmd --reload
```

> **bind Test**  
> centos75 의 경우 별도의 system 입니다.  
> 기존에 사용하던 Google dns 는 주석 처리 합니다.  
> bind 서버를 nameserver 로 지정 합니다.  

```no-highlight
[root@centos75 ~]# vi /etc/resolv.conf
# Generated by NetworkManager
#nameserver 8.8.8.8
nameserver 192.168.0.10
```

* ping Test  
centos75 의 경우 별도의 system 입니다.  
```no-highlight
[root@centos75 ~]# nslookup
> a.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   a.com
Address: 192.168.0.10
> b.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   b.com
Address: 192.168.0.10
> test.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   test.com
Address: 192.168.0.10
>
[root@centos75 ~]# ping test.com
PING test.com (192.168.0.10) 56(84) bytes of data.
64 bytes from 192.168.0.10 (192.168.0.10): icmp_seq=1 ttl=64 time=0.303 ms
^C
--- test.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.303/0.303/0.303/0.000 ms
[root@centos75 ~]#
```

* 역방향 조회  
ip 로 도메인 정보를 확인 합니다.  
```no-highlight
[root@centos75 ~]# nslookup
> 192.168.0.10
Server:         192.168.0.10
Address:        192.168.0.10#53

** server can't find 10.0.168.192.in-addr.arpa.: NXDOMAIN
> 192.168.0.10
Server:         192.168.0.10
Address:        192.168.0.10#53

10.0.168.192.in-addr.arpa       name = ns.test.com.
>
```


> bind chroot 설정  
bind 에서 chroot 환경으로 운영하기 위한 설정 입니다.  

* bind chroot 설정  

> bind-chroot 패키지를 설치 합니다.  
```no-highlight
[root@centos7 ~]# yum install -y bind-chroot
```

* named 데몬이 동작 하였을시 정지 합니다.  
```no-highlight
[root@centos7 ~]# systemctl status named
[root@centos7 ~]# systemctl stop named
[root@centos7 ~]# systemctl disable named
```

* named chroot 환경을 초기화 합니다.  
named 데몬을 사용하지 않고 named-chroot 데몬을 사용합니다.  
```no-highlight
[root@centos7 ~]# /usr/libexec/setup-named-chroot.sh /var/named/chroot on
[root@centos7 ~]# systemctl start named-chroot
[root@centos7 ~]# systemctl enable named-chroot
```

* named chroot 환경 확인  

> named.conf , named.rfc1912.zones 파일 위치  
```no-highlight
[root@centos7 ~]# ls -al /var/named/chroot/etc
total 688
drwxr-x--- 4 root named    187 Oct 17 22:10 .
drwxr-x--- 7 root named     61 Oct 17 21:07 ..
-rw-r--r-- 2 root root     517 May  9 22:11 localtime
drwxr-x--- 2 root named      6 Aug 28 00:40 named
-rw-r----- 1 root named   1693 Oct 17 21:11 named.conf
-rw-r--r-- 1 root named   3923 Aug 28 00:40 named.iscdlv.key
-rw-r----- 1 root named   1474 Oct 17 21:40 named.rfc1912.zones
-rw-r--r-- 1 root named   1587 May 22  2017 named.root.key
drwxr-x--- 3 root named     25 Oct 17 21:07 pki
-rw-r--r-- 1 root root    6545 Jun  7  2013 protocols
-rw-r----- 1 root named    100 Oct 17 21:19 rndc.key
-rw-r--r-- 1 root root  670293 Jun  7  2013 services
[root@centos7 ~]#
```

* 도메인 파일 위치 확인  
```no-highlight
[root@centos7 ~]# ls -al /var/named/chroot/var/named/
total 32
drwxr-x--- 8 root  named  239 Oct 17 21:39 .
drwxr-x--- 5 root  named   52 Oct 17 21:07 ..
-rw-r----- 1 root  named  470 Oct 17 21:29 a.com
-rw-r----- 1 root  named  469 Oct 17 21:29 b.com
drwxr-x--- 7 root  named   61 Oct 17 21:07 chroot
drwxr-x--- 7 root  named   61 Oct 17 21:07 chroot_sdb
drwxrwx--- 2 named named   23 Oct 17 21:19 data
drwxrwx--- 2 named named   31 Oct 17 22:11 dynamic
drwxrwx--- 2 root  named    6 Aug 23  2017 dyndb-ldap
-rw-r----- 1 root  named 2281 May 22  2017 named.ca
-rw-r----- 1 root  named  152 Dec 15  2009 named.empty
-rw-r----- 1 root  named  152 Jun 21  2007 named.localhost
-rw-r----- 1 root  named  168 Dec 15  2009 named.loopback
drwxrwx--- 2 named named    6 Aug 28 00:40 slaves
-rw-r----- 1 root  named  475 Oct 17 21:29 test.com
-rw-r--r-- 1 root  named  401 Oct 17 21:30 test.com.rev
[root@centos7 ~]#
```

* chroot 위치에 dns 파일을 생성 합니다.  

```no-highlight
[root@centos7 ~]# touch /var/named/chroot/var/named/data/cache_dump.db
[root@centos7 ~]# touch /var/named/chroot/var/named/data/named_stats.txt
[root@centos7 ~]# touch /var/named/chroot/var/named/data/named_mem_stats.txt
[root@centos7 ~]# touch /var/named/chroot/var/named/data/named.run
[root@centos7 ~]# touch /var/named/chroot/var/named/data/dynamic
[root@centos7 ~]# mkdir /var/named/chroot/var/named/dynamic
[root@centos7 ~]# touch /var/named/chroot/var/named/dynamic/managed-keys.bind
```


* 권한 변경 및 파일 복사  

```no-highlight
[root@centos7 ~]# chmod -R 777 /var/named/chroot/var/named/data
[root@centos7 ~]# chmod -R 777 /var/named/chroot/var/named/dynamic
[root@centos7 ~]# cp -p /etc/named.conf /var/named/chroot/etc/named.conf
```

* named.conf 설정  

```no-highlight
[root@centos7 ~]# vi /var/named/chroot/etc/named.conf
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };

```

* c.com zone 파일 생성  
설정 위치 /var/named/chroot/var/named

```no-highlight
[root@centos7 ~]# vi /var/named/chroot/etc/named.rfc1912.zones
zone "c.com" In {
        type master;
        file "c.com";
        allow-update { none; };
};

[root@centos7 ~]# cd /var/named/chroot/var/named/
[root@centos7 named]# cp b.com c.com

[root@centos7 named]# vi c.com
$TTL 3H
@       IN SOA  @ ns.c.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        IN      NS      ns.c.com.
        IN      A       192.168.0.10
ns      IN      A       192.168.0.10
www     IN      A       192.168.0.10

[root@centos7 named]# chown root:named c.com
```

* named-chroot 를 Reload 합니다.  
```no-highlight
[root@centos7 ~]# systemctl reload named-chroot
```


* nslookup 테스트  
다른 system 에서 nslookup 과 ping 테스트를 진행 합니다.  
```no-highlight
[root@centos75 ~]# nslookup
> server
Default server: 192.168.0.10
Address: 192.168.0.10#53
> c.com
Server:         192.168.0.10
Address:        192.168.0.10#53

Name:   c.com
Address: 192.168.0.10
>
[root@centos75 ~]# ping c.com
PING c.com (192.168.0.10) 56(84) bytes of data.
64 bytes from ns.test.com (192.168.0.10): icmp_seq=1 ttl=64 time=0.192 ms
64 bytes from ns.test.com (192.168.0.10): icmp_seq=2 ttl=64 time=0.261 ms
^C
--- c.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.192/0.226/0.261/0.037 ms
[root@centos75 ~]#