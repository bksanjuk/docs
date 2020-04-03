+++
title = "IPTABLES"
weight = 7
+++


## What is IPTABLES

{{% notice note %}}
IPTABLES 는 Linux 운영 체제용 으로 제작된 방화벽 유틸 리티 입니다.  
CentOS/RHEL 계열의 경우 7 Version 부터 firewalld 를 사용 합니다.  
{{% /notice %}}


{{% notice tip %}}
CentOS6 Version 까지는 별도의 과정없이 iptables 사용이 가능 하고 CentOS7 Version 의 경우 firewalld mask 및 disable 과정 이 필요 합니다.  
{{% /notice %}}

- firewalld disable (CentOS7 사용시 )  
```no-highlight
[root@centos7 ~]# systemctl stop firewalld
[root@centos7 ~]# systemctl disable firewalld
[root@centos7 ~]# systemctl mask --now firewalld

- iptables install
[root@centos7 ~]# yum install -y iptables-services

- iptables enable
[root@centos7 ~]# systemctl start iptables
[root@centos7 ~]# systemctl enable iptables
```

<br></br>

{{% notice tip %}}
iptables 설정시 설정파일을 직접 수정 또는 command line 에서 명령어로 설정할수 있습니다.  
command line 작업시 service iptables save 를 실행 시켜 작업내용을 저장 해야 합니다.  
{{% /notice %}}

- iptables 설정 파일  
```no-highlight
[root@centos7 ~]# vi /etc/sysconfig/iptables
# sample configuration for iptables service
# you can edit this manually or use system-config-firewall
# please do not ask us to add additional ports/services to this default configuration
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```
{{% notice warning %}}
한글 번역이 매끄럽지 못한 부분이 있습니다.
{{% /notice %}}

{{% notice note %}}
iptables table 에는 5가지가 존재 합니다. 일반적으로 filter 를 많이 사용 합니다.  
{{% /notice %}}

<br></br>

- **table 종류**  

|iptables table  |  |
|---|---|
|filter  |네트워크 패킷 처리를 위한 기본 테이블  |
|nat  |새 연결을 생성하고 NAT에 사용되는 패킷을 변경하는데 사용합니다.    |
|mangle  |특정 유형의 패킷 변화에 사용됩니다.  |
|raw  |NOTRACK 대상과 결합하여 연결 추적에서 제외하는데 사용됩니다.   |
|security  |SECMARK 및 CONNSECMARK 대상에서 활성화 한 규칙과 같은 MAC 네트워크 규칙에 사용됩니다.   |


<br></br>

- **filter 및 nat table**  
  
|filter table Chain  |  |
|---|---|
|INPUT  |호스트를 대상으로 하는 네트워크 패킷에 적용됩니다.  |
|OUTPUT  |로컬로 생성 된 네트워크 패킷에 적용됩니다.  |
|FORWARD  |호스트를 통해 라우팅 되는 네트워크 패킷에 적용됩니다.  |


<br></br>
 
|nat table Chain      |
|---|---|
|PREOUTING   |네트워크 패킷이 도착하면 적용됩니다.   |
|OUTPUT   |로컬로 생성된 네트워크 패킷이 전송되기 전에 적용됩니다.   |
|POSTROUTING   |네트워크 패킷을 보내기 전에 적용합니다.   |

<br></br>

- command line 을 이용한 iptables 설정  

|옵션   |설명   |
|---|---|
|-A   |새로운 규칙을 iptables 마지막에 등록   |
|-I   |새로운 규칙을 iptables 첫 부분에 등록    |
|-D   |규칙을 삭제   |
|-C   |규칙을 삭제   |
|-R   |새로운 규칙으로 교체   |
|-L   |규칙을 출력   |
|-F   |모든 규칙을 삭제   |
|-Z   |모든 Chain의 패킷과 바이트 카운터 값을 0으로 변경   |
|-N   |새로운 Chain을 생성    |
|-X   |Chain을 삭제   |
|-P   |기본정책을 변경    |



{{% notice tip %}}
Default iptables 설정시 -I 옵션을 사용 합니다.  
-A 옵션 사용시 마지막 라인에 적용 되기 때문에 해당 포트로 접속을 할수 없습니다.  

{{% /notice %}}

- well-known port  

|Service   |port   | tcp or udp   |
|---|---|---|
|http   |80   |tcp   |
|https   |443   |tcp   |
|mysql   |3306   |tcp   |
|tomcat   |8080   |tcp   |
|dns   |53   |tcp/udp   |
|ftp(data)   |20   |tcp   |
|ftp(control)   |21   |tcp   |

- 서비스 포트 허용  

```no-highlight
[root@centos7 ~]# iptables -I INPUT -p tcp -m state --state NEW --dport 80 -j ACCEPT

최상단에 http 가 추가된것을 확인 할수 있습니다.  
[root@centos7 ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:http
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
[root@centos7 ~]#
```

<br></br>

{{% notice warning %}}
iptables Default 사용시 -A 옵션 사용시 REJECT 가 될수 있습니다.  

{{% /notice %}}

```no-highlight
-A 옵션 사용시 최하단에 추가가 됩니다.  
http 포트 오픈전 모든 접속을 REJECT 하기 때문에 HOST에 접근할수 없습니다.  

[root@centos7 ~]# iptables -A INPUT -p tcp -m state --state NEW --dport 80 -j ACCEPT
[root@centos7 ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     icmp --  anywhere             anywhere
ACCEPT     all  --  anywhere             anywhere
ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:ssh
REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited    <--- REJECT
ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:http              <--- 목적지가 http 일경우 접속 허용  

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination
REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
[root@centos7 ~]#
```

{{% notice info %}}
만약 특정대역으로 으로 허용 한다면 /24 /16 대역으로 설정 하며, 특정 아이피만 허용 하는경우는 192.168.0.20 으로 지정 합니다.  
{{% /notice %}}

- 특정 아이피 대역 허용  
```no-highlight
[root@centos7 ~]# iptables -F
[root@centos7 ~]# iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
[root@centos7 ~]# iptables -A INPUT -s 192.168.0.0/24 -j ACCEPT
[root@centos7 ~]# iptables -A INPUT -j DROP
[root@centos7 ~]# service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]

[root@centos7 ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
ACCEPT     all  --  192.168.0.0/24       anywhere
DROP       all  --  anywhere             anywhere

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
[root@centos7 ~]#
```

- 특정아이피 특정 port 허용  
```no-highlight
[root@centos7 ~]# iptables -I INPUT -p tcp -s 10.10.10.10 --dport 22 -j ACCEPT
[root@centos7 ~]# service iptables save
```