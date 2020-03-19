+++
title = "Locale"
weight = 5
+++
## Locale

* locale 을 확인합니다.
```no-highlight
[root@localhost ~]# locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
[root@localhost ~]#
```

* localectl 명령어를 이용하여 ko_KR.utf8 로 변경합니다.  
/etc/locale.conf 에 정상적으로 적용되었는지 확인 합니다.  
```no-highlight
[root@centos7 ~]# localectl set-locale LANG=ko_KR.utf8
[root@centos7 ~]# cat /etc/locale.conf
LANG=ko_KR.utf8
[root@centos7 ~]#
```
<br></br>
