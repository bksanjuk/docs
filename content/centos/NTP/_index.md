+++
title = "NTP"
weight = 10
+++

## NTP

> NTP (Network Time protocol) 로 System 에 시간을 동기화 하는 역활을 합니다.  
> CentOS7 / RHEL7 에서는 ntpd 대신 chronyd 를 사용 합니다.  
<br></br>

* chronyd 패키지가 설치 되었는지 확인합니다.
```no-highlight
[root@centos7 ~]# rpm -aq |grep -i chrony

chrony 패키지를 설치 합니다.
[root@centos7 ~]# yum install -y chrony
```
> chrony 의 경우 설정 파일이 /etc/chrony.conf 입니다.


* chrony.conf 파일
```no-highlight
[root@centos7 ~]# cat /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

# Record the rate at which the system clock gains/losses time.
driftfile /var/lib/chrony/drift

# Allow the system clock to be stepped in the first three updates
# if its offset is larger than 1 second.
makestep 1.0 3

# Enable kernel synchronization of the real-time clock (RTC).
rtcsync

# Enable hardware timestamping on all interfaces that support it.
#hwtimestamp *

# Increase the minimum number of selectable sources required to adjust
# the system clock.
#minsources 2

# Allow NTP client access from local network.
#allow 192.168.0.0/16

# Serve time even if not synchronized to a time source.
#local stratum 10

# Specify file containing keys for NTP authentication.
#keyfile /etc/chrony.keys

# Specify directory for log files.
logdir /var/log/chrony
```

> server 옵션을 사용하여 time Server 를 지정 할수 있습니다.  
> ex) http://www.pool.ntp.org/zone/kr  
> server 1.kr.pool.ntp.org  
> server 0.asia.pool.ntp.org  
> server 1.asia.pool.ntp.org  
> chrony Server 로 사용할 경우 allow 에서 Client network 를 설정해 줍니다.  
> allow 192.168.0.0/24  






* chronyd 구동

```no-highlight
[root@centos7 ~]# systemctl start chronyd
```


* chronyd Time sync 확인 (동기화 확인)

```no-highlight
[root@centos7 ~]# chronyc tracking
Reference ID    : 6AF7F86A (106.247.248.106)
Stratum         : 3
Ref time (UTC)  : Sat Sep 29 11:46:08 2018
System time     : 0.000986114 seconds slow of NTP time
Last offset     : -0.000145085 seconds
RMS offset      : 0.000665187 seconds
Frequency       : 96.481 ppm fast
Residual freq   : -0.035 ppm
Skew            : 1.050 ppm
Root delay      : 0.014674330 seconds
Root dispersion : 0.029061176 seconds
Update interval : 64.4 seconds
Leap status     : Normal
[root@centos7 ~]#
```
* 동기화 소스 확인
```no-highlight
[root@centos7 ~]# chronyc sources -v
210 Number of sources = 4

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current synced, '+' = combined , '-' = not combined,
| /   '?' = unreachable, 'x' = time may be in error, '~' = time too variable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ 203.217.204.135               2   6   377     5    +13ms[  +13ms] +/-  101ms
^+ dadns.cdnetworks.co.kr        2   6   377     7  -7304us[-7342us] +/-   73ms
^+ send.mx.cdnetworks.com        2   6   377     5  -1727us[-1765us] +/-   67ms
^* 106.247.248.106               2   6   377     4  +1070ns[  -38us] +/-   40ms
[root@centos7 ~]#
```


* time sync 명령어
```no-highlight
[root@rhel7 ~]# chronyc -a makestep
200 OK
[root@rhel7 ~]# date
Sat Sep 29 21:03:00 KST 2018
[root@rhel7 ~]#
```

* firewalld 방화벽 추가
chronyd Server / Client  양쪽에서 추가 합니다.
```no-highlight
[root@centos7 ~]# firewall-cmd --add-service=ntp --permanent
[root@centos7 ~]# firewall-cmd --reload
```

<br></br>

