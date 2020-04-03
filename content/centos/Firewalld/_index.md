+++
title = "Firewalld"
weight = 14
+++


## Firewalld  
> CentOS7 / RHEL7 에서는 방화벽으로 Firewalld 를 사용합니다.  
> firewalld 의 경우 zone 을 생성 하여 방화벽 Rule 을 설정 할수 있으며 zone 에 interface(Nic)를 지정 할수 있습니다.  


- OS 설치시 기본으로 작동하며 동작 확인은 아래와 같이 할수 있습니다.
```no-highlight
[root@centos7 ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since 일 2018-09-30 12:11:49 KST; 34min ago
     Docs: man:firewalld(1)
 Main PID: 654 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─654 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

 9월 30 12:11:48 centos7 systemd[1]: Starting firewalld - dynamic firewall daemon...
 9월 30 12:11:49 centos7 systemd[1]: Started firewalld - dynamic firewall daemon.
[root@centos7 ~]#
```

- firewalld Daemon Disable 및 Daemon stop  
> 별도의 방화벽 장비를 사용하거나 방화벽을 사용 하지 않을 경우  
> firewalld Daemon 을 비활성 / 정지 하시면 됩니다.  
```no-highlight
[root@centos7 ~]# systemctl disable firewalld && systemctl stop firewalld
```

* firewalld.conf 설정
설정파일의 경우 /etc/firewalld/firewalld.conf 파일에서 설정 할수 있습니다.
```no-highlight
[root@centos7 ~]# cat /etc/firewalld/firewalld.conf
# firewalld config file

# default zone
# The default zone used if an empty zone string is used.
# Default: public
DefaultZone=public

# Minimal mark
# Marks up to this minimum are free for use for example in the direct
# interface. If more free marks are needed, increase the minimum
# Default: 100
MinimalMark=100

# Clean up on exit
# If set to no or false the firewall configuration will not get cleaned up
# on exit or stop of firewalld
# Default: yes
CleanupOnExit=yes

# Lockdown
# If set to enabled, firewall changes with the D-Bus interface will be limited
# to applications that are listed in the lockdown whitelist.
# The lockdown whitelist file is lockdown-whitelist.xml
# Default: no
Lockdown=no

# IPv6_rpfilter
# Performs a reverse path filter test on a packet for IPv6. If a reply to the
# packet would be sent via the same interface that the packet arrived on, the
# packet will match and be accepted, otherwise dropped.
# The rp_filter for IPv4 is controlled using sysctl.
# Default: yes
# Default: yes
IPv6_rpfilter=yes

# IndividualCalls
# Do not use combined -restore calls, but individual calls. This increases the
# time that is needed to apply changes and to start the daemon, but is good for
# debugging.
# Default: no
IndividualCalls=no

# LogDenied
# Add logging rules right before reject and drop rules in the INPUT, FORWARD
# and OUTPUT chains for the default rules and also final reject and drop rules
# in zones. Possible values are: all, unicast, broadcast, multicast and off.
# Default: off
LogDenied=off

# AutomaticHelpers
# For the secure use of iptables and connection tracking helpers it is
# recommended to turn AutomaticHelpers off. But this might have side effects on
# other services using the netfilter helpers as the sysctl setting in
# /proc/sys/net/netfilter/nf_conntrack_helper will be changed.
# With the system setting, the default value set in the kernel or with sysctl
# will be used. Possible values are: yes, no and system.
# Default: system
AutomaticHelpers=system
[root@centos7 ~]#
```

* 방화벽 상태 확인
```no-highlight
[root@centos7 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens33 ens34 bond0
  sources:
  services: ssh dhcpv6-client ntp
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@centos7 ~]#
```

* Default zone 확인
```no-highlight
[root@centos7 ~]# firewall-cmd --get-default-zone
public
```
* public zone 설정파일
zone 설정 파일의 경우 /etc/firewalld/zones 디렉토리에 위치 하고 있습니다.
```no-highlight
[root@centos7 ~]# cat /etc/firewalld/zones/public.xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <service name="ntp"/>
</zone>
[root@centos7 ~]#
```

* 사용 가능한 zone 목록 확인
```no-highlight
[root@centos7 ~]# firewall-cmd --get-zones
block dmz drop external home internal public trusted work
```

* 사용할수 있는 Service 목록 확인
```no-highlight
[root@centos7 ~]# firewall-cmd --get-services
```

* Service port 추가
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-service=http
```

* Service port 삭제
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --remove-service=http
```


* zone 생성
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --new-zone=web-service
```

* 설정적용
```no-highlight
root@centos7 ~]# firewall-cmd --reload
```

* web-service zone 에 Service 추가
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=web-service --add-service=http
[root@centos7 ~]# firewall-cmd --permanent --zone=web-service --add-service=https
[root@centos7 ~]# firewall-cmd --reload
```



* zone 삭제
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --delete-zone=web-service
```

* public zone에 특정 포트허용
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-port=20201/tcp
[root@centos7 ~]# firewall-cmd --reload
[root@centos7 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens33 ens34 bond0
  sources:
  services: ssh dhcpv6-client ntp
  ports: 20201/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@centos7 ~]#
```
ports 라인에 20201 ports 가 추가된것을 확인 할수 있습니다.

* public zone에 특정 포트 삭제
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --remove-port=20201/tcp
[root@centos7 ~]# firewall-cmd --reload
[root@centos7 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens33 ens34 bond0
  sources:
  services: ssh dhcpv6-client ntp
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@centos7 ~]#
```

* public zone 에 포트범위로 접근 허용
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-port=2000-2200/tcp
[root@centos7 ~]# firewall-cmd --reload
```

* IP 대역으로 접근 허용
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-source=192.168.0.0/24 --add-port=22/tcp
```

{{% notice tip %}}
--add-service 를 이용하여 Service 별로 허용해 줄수도 있습니다.  
{{% /notice %}}

* http 서비스 허용  
```no-highlight
[root@CentOS7 ~]# firewall-cmd --permanent --add-service=http
[root@CentOS7 ~]# firewall-cmd --permanent --add-service=https
[root@CentOS7 ~]# firewall-cmd --reload
```

* NFS 서비스 허용  
```no-highlight
[root@CentOS7 ~]# firewall-cmd --permanent --add-service=nfs
[root@CentOS7 ~]# firewall-cmd --permanent --add-service=rpc-bind
[root@CentOS7 ~]# firewall-cmd --reload
```

* SAMBA 서비스 허용  
```no-highlight
[root@CentOS7 ~]# firewall-cmd --permanent --add-service=samba
[root@CentOS7 ~]# firewall-cmd --reload
```

<br></br>

