+++
title = "Hostname"
weight = 4
+++

## Hostname
> /etc/hostname 의 경우 시스템 실행시 hostname 을 고정하기 위하여 사용합니다.  
> hostnamectl 명령어로 hostname 을 설정 할수 있습니다.  

* /etc/hostname 설정  
```no-highlight
test@ubuntu:~$ sudo vi /etc/hostname
test@ubuntu:~$ sudo hostnamectl set-hostname ubuntu-test
```



