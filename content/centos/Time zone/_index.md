+++
title = "Time Zone"
weight = 6
+++

## Time zone

* time zone 확인
```no-highlight
[root@centos7 ~]# timedatectl
      Local time: 토 2018-09-29 09:34:53 KST
  Universal time: 토 2018-09-29 00:34:53 UTC
        RTC time: 토 2018-09-29 00:34:53
       Time zone: Asia/Seoul (KST, +0900)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
[root@centos7 ~]#
```


* America/Toronto Time zone 으로 변경
```no-highlight
[root@centos7 ~]# timedatectl list-timezones |grep -i toronto
America/Toronto
[root@centos7 ~]# timedatectl set-timezone America/Toronto

적용한 time zone 확인
[root@centos7 ~]# timedatectl
      Local time: 금 2018-09-28 20:38:12 EDT
  Universal time: 토 2018-09-29 00:38:12 UTC
        RTC time: 토 2018-09-29 00:38:12
       Time zone: America/Toronto (EDT, -0400)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: yes
 Last DST change: DST began at
                  일 2018-03-11 01:59:59 EST
                  일 2018-03-11 03:00:00 EDT
 Next DST change: DST ends (the clock jumps one hour backwards) at
                  일 2018-11-04 01:59:59 EDT
                  일 2018-11-04 01:00:00 EST
[root@centos7 ~]#
```
