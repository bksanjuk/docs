+++
title = "System Upgrade"
weight = 3
+++

## System Upgrade

> System Package upgrade  
> 시스템의 모든 패키지를 업그레이드 합니다.  
> 16.04 LTS -> 18.04 LTS upgrade  

- kernel-upgrade 시 upgrade 된 커널을 적용하기 위해서는 시스템 리부팅이 필요합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get upgrade -y
test@ubuntu:~$ sudo init 6
```

* 모든 패키지 업데이트  

```no-highlight
test@ubuntu:~$ sudo apt update && sudo apt dist-upgrade && sudo apt autoremove
update-manager-core 패키지 설치
test@ubuntu:~$ sudo apt-get install update-manager-core
Prompt=lts 확인
test@ubuntu:~$ cat /etc/update-manager/release-upgrades |grep -i prompt
Prompt=lts
```  

* 18.04 Upgrade  

```no-highlight

test@ubuntu:~$ sudo do-release-upgrade -d
Reading cache

Checking package manager

Continue running under SSH?

This session appears to be running under ssh. It is not recommended
to perform a upgrade over ssh currently because in case of failure it
is harder to recover.

If you continue, an additional ssh daemon will be started at port
'1022'.
Do you want to continue?

Continue [yN] y

Starting additional sshd

To make recovery in case of failure easier, an additional sshd will
be started on port '1022'. If anything goes wrong with the running
ssh you can still connect to the additional one.
If you run a firewall, you may need to temporarily open this port. As
this is potentially dangerous it's not done automatically. You can
open the port with e.g.:
'iptables -I INPUT -p tcp --dport 1022 -j ACCEPT'

To continue please press [ENTER]

```

* Keep the local version currently installed 를 선택 합니다.  
![업그레이드1](/ubuntu/ubuntu_tmp/upgrade/k1.png)


```no-highlight
System upgrade is complete.

Restart required

To finish the upgrade, a restart is required.
If you select 'y' the system will be restarted.

Continue [yN] y
```  
<br></br>

* System rebooting 후 확인  
```no-highlight
test@ubuntu:~$ cat /etc/lsb-release  
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.1 LTS"  
test@ubuntu:~$  
```
<br></br>
