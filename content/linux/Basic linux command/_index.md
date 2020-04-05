+++
title = "Basic Linux Command"
weight = 5
+++

## Basic Linux Command

{{% notice note %}}
리눅스 운영시 필요한 기본적인 명령어를 소개 합니다.  
{{% /notice %}}

{{% notice info %}}
일부 명령어는 패키지 설치가 필요합니다.  
{{% /notice %}}

## 파일 및 디렉토리 명령어  

> **ls 명령어**는 파일 엔트리 정보를 표시 합니다.  

|옵션   |설명   |
|---|---|
|-a   | all 옵션으로 모든 파일 과 디렉토리 출력 , (.) 숨김 파일 및 디렉토리 표시   |
|-F   | classify 옵션으로  엔트리분류 기호를 표시하여 출력 한다. 디렉토리 / , 심볼릭 링크 @ , 실행가능 파일에는 * 표시합니다.   |
|-s   | size 옵션으로 용량을 표시합니다.   |
|-l   | 파일타입,퍼미션,하드링크수, 오너, 그룹 파일 사이즈, 최종 갱신일을 표시 합니다.   |

```no-highlight
# -a 옵션 사용시
[root@linux ~]# ls -a
.   anaconda-ks.cfg  .bash_logout   .bashrc  .lesshst        .pki  .tcshrc
..  .bash_history    .bash_profile  .cshrc   .mysql_history  .rnd
[root@linux ~]#


# -F 옵션 사용시
[root@linux ~]# ls -F /etc/
adjtime                  cron.d/        DIR_COLORS               fuse.conf    hosts         ld.so.cache     makedumpfile.conf.sample  odbcinst.ini  plymouth/        rc2.d@            rsyslog.d/
aliases                  cron.daily/    DIR_COLORS.256color      gcrypt/      hosts.allow   ld.so.conf      man_db.conf               openldap/     pm/              rc3.d@            rwtab
aliases.db               cron.deny      DIR_COLORS.lightbgcolor  gnupg/       hosts.deny    ld.so.conf.d/   mime.types                opt/          polkit-1/        rc4.d@            rwtab.d/
alternatives/            cron.hourly/   dracut.conf              GREP_COLORS  httpd/        libaudit.conf   mke2fs.conf               os-release    popt.d/          rc5.d@            sasl2/
anacrontab               cron.monthly/  dracut.conf.d/           groff/       init.d@       libnl/          modprobe.d/               pam.d/        postfix/         rc6.d@            securetty
asound.conf              crontab        e2fsck.conf              group        inittab       libuser.conf    modules-load.d/           passwd        ppp/             rc.d/             security/
~중략
[root@linux ~]#


# -s 옵션 사용시
[root@linux ~]# ls -s /etc/
total 1200
  4 adjtime                    0 cron.hourly                0 environment    4 gshadow        4 krb5.conf                   8 man_db.conf          4 pam.d             4 printcap
  4 aliases                    0 cron.monthly               4 ethertypes     4 gshadow-       0 krb5.conf.d                52 mime.types           4 passwd            4 profile
 12 aliases.db                 4 crontab                    0 exports        0 gss           28 ld.so.cache                 4 mke2fs.conf          4 passwd-           0 profile.d
  0 alternatives               0 cron.weekly                0 favicon.png    4 host.conf      4 ld.so.conf                  0 modprobe.d           0 pear              8 protocols
  4 anacrontab                 0 crypttab                   4 filesystems    4 hostname       0 ld.so.conf.d                0 modules-load.d       4 pear.conf         0 python
  4 asound.conf                4 csh.cshrc                  0 firewalld      4 hosts          4 libaudit.conf               0 motd                 4 php.d             0 rc0.d
  0 audisp                     4 csh.login                  0 fonts          4 hosts.allow    0 libnl                       0 mtab                64 php.ini           0 rc1.d
  0 audit                      0 dbus-1                     4 fstab          4 hosts.deny     4 libuser.conf                4 my.cnf               4 php-zts.d         0 rc2.d
  0 bash_completion.d          0 default                    4 fuse.conf      0 httpd          4 locale.conf                 0 my.cnf.d             0 pkcs11            0 rc3.d
  4 bashrc                     0 depmod.d                   0 gcrypt         0 init.d         0 localtime                   0 NetworkManager       0 pki               0 rc4.d
  ~중략
[root@linux ~]#


# -l 옵션 사용시
[root@linux ~]# ls -al /etc/
total 1216
drwxr-xr-x. 79 root root   8192 Mar 31 05:10 .
dr-xr-xr-x. 19 root root    255 Mar 27 09:23 ..
-rw-r--r--.  1 root root     16 Mar 26 08:23 adjtime
-rw-r--r--.  1 root root   1518 Jun  7  2013 aliases
-rw-r--r--.  1 root root  12288 Mar 26 08:27 aliases.db
drwxr-xr-x.  2 root root    236 Mar 26 08:22 alternatives
-rw-------.  1 root root    541 Aug  9  2019 anacrontab
-rw-r--r--.  1 root root     55 Aug  8  2019 asound.conf
drwxr-x---.  3 root root     43 Mar 26 08:22 audisp
drwxr-x---.  3 root root     83 Mar 26 08:27 audit
drwxr-xr-x.  2 root root     44 Mar 27 08:00 bash_completion.d
-rw-r--r--.  1 root root   2853 Oct 31  2018 bashrc
drwxr-xr-x.  2 root root      6 Aug  8  2019 binfmt.d
-rw-r--r--.  1 root root     37 Sep  5  2019 centos-release
-rw-r--r--.  1 root root     51 Sep  5  2019 centos-release-upstream
drwxr-xr-x.  2 root root      6 Aug  4  2017 chkconfig.d
drwxr-xr-x.  2 root root     21 Mar 26 08:22 cron.d
drwxr-xr-x.  2 root root     42 Mar 26 08:22 cron.daily
-rw-------.  1 root root      0 Aug  9  2019 cron.deny
lrwxrwxrwx.  1 root root     56 Mar 26 08:22 favicon.png -> /usr/share/icons/hicolor/16x16/apps/fedora-logo-icon.png
-rw-r--r--.  1 root root     70 Oct 31  2018 filesystems
drwxr-x---.  7 root root    133 Mar 26 08:22 firewalld
drwxr-xr-x.  3 root root     38 Mar 27 08:01 fonts
-rw-r--r--.  1 root root    501 Mar 26 08:21 fstab
-rw-r--r--.  1 root root     38 Oct 30  2018 fuse.conf
drwxr-xr-x.  2 root root      6 Aug  3  2017 gcrypt
~ 중략

```

> **find 명령어**는 파일 및 디렉토리 검색시 사용 합니다.  
> 자세한 옵션은 man find 로 확인하시기 바랍니다.  

```no-highlight
# / 디렉토리에서 ssh 문자가 포함된 파일과 디렉토리 검색
[root@linux ~]# find / -name "ssh*"
/run/sshd.pid
/sys/fs/selinux/booleans/ssh_chroot_rw_homedirs
/sys/fs/selinux/booleans/ssh_sysadm_login
/sys/fs/selinux/booleans/ssh_keysign
/sys/fs/cgroup/devices/system.slice/sshd.service
/sys/fs/cgroup/systemd/system.slice/sshd.service
/etc/systemd/system/multi-user.target.wants/sshd.service
/etc/sysconfig/sshd
/etc/selinux/targeted/active/modules/100/ssh
/etc/ssh
/etc/ssh/sshd_config
/etc/ssh/ssh_config
~중략


# / 디렉토리에서 ssh 가 포함된 디렉토리만 검색  
[root@linux ~]# find / -name "ssh*" -type d
/sys/fs/cgroup/devices/system.slice/sshd.service
/sys/fs/cgroup/systemd/system.slice/sshd.service
/etc/selinux/targeted/active/modules/100/ssh
/etc/ssh
/var/empty/sshd
[root@linux ~]#

# user01 소유권을 가진 파일 검색  
[root@linux ~]# find / -user user01
/var/spool/mail/user01
/usr/lib/systemd/system/test.service
/home/user01
/home/user01/.bash_logout
/home/user01/.bash_profile
/home/user01/.bashrc
[root@linux ~]#

```


> **pwd 명령어**는 현재 작업중인 디렉토리를 출력 합니다.  

```no-highlight
[root@linux ~]# pwd
/root
[root@linux ~]#
```



> **cd 명령어**는 디렉토리 이동시 사용합니다.  

```no-highlight
[root@linux ~]# cd /usr/local/etc
[root@linux etc]#
```


> **cp 명령어**는 파일 복사 및 디렉토리 복사시 사용합니다.  

|옵션   |설명   |
|---|---|
|-a   |archive 옵션으로 복사할 원파일 구성과 속성을 유지하여 복사 합니다.   |
|-d   |심볼릭 링크 및 하드링크 상태를 유지 하여 복사 합니다.   |
|-f   |force 옵션으로 덮어쓰기 하여 복사 합니다.   |
|-r   |디렉토리를 복사 할때 사용 합니다.   |
|-p   |원본파일 그룹,권한 시간 정보 및 소유주를 유지 하여 복사 합니다.   |

```no-highlight
# -arp 옵션 사용

[root@linux ~]# cp -arp /etc .
```



> **mv 명령어**는 파일 이동시 , 파일명 변경시 사용합니다.  

```no-highlight
[root@linux ~]# mv file1 file2
```



> **mkdir 명령어**는 디렉토리 생성시 사용 합니다.  

```no-highlight

[root@linux ~]# mkdir /data
```


> **rmdir 명령어**는 디렉토리 삭제시 사용 합니다. rm -r 옵션을 사용하여 디렉토리를 삭제 할수 있습니다.  

```no-highlight
[root@linux ~]# rmdir /data
```

> **rm 명령어**는 파일 삭제 및 디렉토리 삭제 시 사용 합니다.  

|옵션   |설명   |
|---|---|
|-r   |디렉토리 삭제시 사용합니다.   |
|-i   |삭제 여부를 사용자에게 물어봅니다.   |
|-f   |강제로 파일을 삭제 할때 사용합니다.   |
|-v   |삭제 하는 내용을 보여줍니다.   |

```no-highlight
[root@linux ~]# rm -rf /data
```


> **touch 명령어**는 빈파일 생성시 사용합니다.  

```no-highlight
[root@linux ~]# touch file
```

> **fallocate 명령어**는 용량을 지정하여 파일을 생성 할수 있습니다.  
> -l 옵션을 사용하여 size 를 지정하여 파일을 생성 합니다.  

```no-highlight
[root@linux ~]# fallocate -l 1G file
```
> **du 명령어**는 파일 및 디렉토리 사용량 확인시 사용합니다.  

|옵션   |설명   |
|---|---|
|-k   |K바이트 단위로 표시 합니다.   |
|-m   |M바이트 단위로 표시 합니다.   |
|-h   |표시내용에 단위를 붙여 알기 쉬운형태로 출력합니다.   |
|-s   |파일 및 디렉토리 디스크 사용량 합계만 출력합니다.   |

```no-highlight
[root@linux ~]# du -sh /var
502M    /var
[root@linux ~]#
```

> **tar 명령어**는 archive 생성시 사용합니다.  
일반적으로 디렉토리 또는 파일을 묶을때 사용합니다.  

|옵션   |설명   |
|---|---|
|-x   |extract 옵션으로 archive 에서 파일 추출시 사용합니다.    |
|-v   |verbose 옵션으로 처리 과정을 출력합니다.   |
|-f   |archive 파일에 읽기,쓰기를 실행합니다.   |
|-z   |gzip 옵션으로 gzip 을 사용합니다.     |
|-c   |create 옵션으로 archive 파일 생성시 사용합니다.   |
|-t   |archive 파일의 내용을 확인할때 사용합니다.    |

```no-highlight
# archive 파일 작성시 -cvf 옵션 사용   
[root@linux ~]# tar cvf network-scripts.tar /etc/sysconfig/network-scripts


# archive 파일의 내용을 확인 -tvf 옵션 사용 
[root@linux ~]# tar tvf network-scripts.tar
drwxr-xr-x root/root         0 2020-03-26 08:30 etc/sysconfig/network-scripts/
-rw-r--r-- root/root       254 2019-03-29 20:56 etc/sysconfig/network-scripts/ifcfg-lo
lrwxrwxrwx root/root         0 2020-03-26 08:22 etc/sysconfig/network-scripts/ifdown -> ../../../usr/sbin/ifdown
-rwxr-xr-x root/root       654 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-bnep
-rwxr-xr-x root/root      6532 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-eth
-rwxr-xr-x root/root       781 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-ippp
-rwxr-xr-x root/root      4540 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-ipv6
lrwxrwxrwx root/root         0 2020-03-26 08:22 etc/sysconfig/network-scripts/ifdown-isdn -> ifdown-ippp
-rwxr-xr-x root/root      2130 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-post
-rwxr-xr-x root/root      1068 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-ppp
-rwxr-xr-x root/root       870 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-routes
-rwxr-xr-x root/root      1456 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-sit
-rwxr-xr-x root/root      1462 2019-03-29 20:56 etc/sysconfig/network-scripts/ifdown-tunnel
lrwxrwxrwx root/root         0 2020-03-26 08:22 etc/sysconfig/network-scripts/ifup -> ../../../usr/sbin/ifup
-rwxr-xr-x root/root     12415 2019-03-29 20:56 etc/sysconfig/network-scripts/ifup-aliases
~ 중략
-rwxr-xr-x root/root      1876 2017-03-18 02:41 etc/sysconfig/network-scripts/ifup-TeamPort
-rw-r--r-- root/root       168 2020-03-26 08:30 etc/sysconfig/network-scripts/ifcfg-ens33
[root@linux ~]#

# archive 파일 추출시 -xvf 옵션 사용  
[root@linux ~]# tar xvf network-scripts.tar

```
> **zip 명령어**는 zip 형식으로 archive 파일을 생성 할때 사용 합니다.  
> 별도의 zip 패키지가 필요 하며 CentOS 에서는 yum install zip 으로 설치 할수 있습니다.  

```no-highlight
# 디렉토리 포함 압축시  
[root@linux ~]# zip -r network-scripts.zip /etc/sysconfig/network-scripts
  adding: etc/sysconfig/network-scripts/ (stored 0%)
  adding: etc/sysconfig/network-scripts/ifcfg-lo (deflated 25%)
  adding: etc/sysconfig/network-scripts/ifdown (deflated 59%)
  adding: etc/sysconfig/network-scripts/ifdown-bnep (deflated 43%)
  adding: etc/sysconfig/network-scripts/ifdown-eth (deflated 64%)
  adding: etc/sysconfig/network-scripts/ifdown-ippp (deflated 54%)
  adding: etc/sysconfig/network-scripts/ifdown-ipv6 (deflated 62%)
  adding: etc/sysconfig/network-scripts/ifcfg-ens33 (deflated 18%)
  ~중략
[root@linux ~]# 
```

> **unzip 명령어**는 zip 형식의 archive 파일 압축해제시 사용합니다.  
> 별도의 unzip 패키지가 필요 하며 CentOS 에서는 yum instll unzip 으로 설치 할수 있습니다.  


```no-highlight
[root@linux ~]# unzip network-scripts.zip
Archive:  network-scripts.zip
   creating: etc/sysconfig/network-scripts/
  inflating: etc/sysconfig/network-scripts/ifcfg-lo
  inflating: etc/sysconfig/network-scripts/ifdown
  inflating: etc/sysconfig/network-scripts/ifdown-bnep
  inflating: etc/sysconfig/network-scripts/ifdown-eth
  inflating: etc/sysconfig/network-scripts/ifdown-ippp
  inflating: etc/sysconfig/network-scripts/ifcfg-ens33
  ~중략
[root@linux ~]#

```

> **scp 명령어**는 secure copy 로 원격지 파일을 복사 하는 프로그램 입니다.  

```no-highlight
# 원격지로 파일 복사 하기  
[root@linux ~]# scp anaconda-ks.cfg root@192.168.0.100:/data/

# 원격지에서 파일 가지고 오기  
[root@linux ~]# scp root@192.168.0.100:/data/db.tar /root/

# ssh port 지정 하여 파일 복사  
[root@linux ~]# scp -P 20201 network-scripts.zip user01@sanbsd.talkzz.com:/home/user01
```




## 텍스트 파일  

> **cat 명령어**는 텍스트 파일 확인시 사용하는 명령어 입니다.  

```no-highlight
[root@linux ~]# cat anaconda-ks.cfg
#version=DEVEL
# System authorization information
auth --enableshadow --passalgo=sha512
# Use CDROM installation media
cdrom
# Use graphical install
graphical
# Run the Setup Agent on first boot
firstboot --enable
ignoredisk --only-use=sda
# Keyboard layouts
keyboard --vckeymap=us --xlayouts='us'
# System language
lang en_US.UTF-8

# Network information
network  --bootproto=dhcp --device=ens33 --onboot=off --ipv6=auto --no-activate
network  --hostname=localhost.localdomain

# Root password
rootpw --iscrypted enPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenPbW1$BywHeTQenP
# System services
services --disabled="chronyd"
# System timezone
timezone Asia/Seoul --isUtc --nontp
# System bootloader configuration
bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=sda
~중략
```

> **zcat 명령어**는 gzip 형식이나 .Z 압축파일의 내용을 확인 할수 있는 명령어 입니다.  

```no-highlight
[root@linux log]# zcat messages.gz
Mar 31 04:37:58 centos7 systemd: Started File System Check on /dev/disk/by-uuid/e0e7347b-47a1-49bc-aa35-48c065181604.
Mar 31 04:37:58 centos7 kernel: e1000 0000:02:01.0 eth0: (PCI:66MHz:32-bit) 00:0c:29:a5:b2:43
Mar 31 04:37:58 centos7 kernel: e1000 0000:02:01.0 eth0: Intel(R) PRO/1000 Network Connection
Mar 31 04:37:58 centos7 kernel: ahci 0000:02:05.0: AHCI 0001.0300 32 slots 30 ports 6 Gbps 0x3fffffff impl SATA mode
Mar 31 04:37:58 centos7 kernel: ahci 0000:02:05.0: flags: 64bit ncq clo only
Mar 31 04:37:58 centos7 kernel: scsi host3: ahci
Mar 31 04:37:58 centos7 kernel: scsi host4: ahci
Mar 31 04:37:58 centos7 kernel: ata20: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5ee980 irq 56
Mar 31 04:37:58 centos7 kernel: ata21: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5eea00 irq 56
Mar 31 04:37:58 centos7 kernel: ata22: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5eea80 irq 56
Mar 31 04:37:58 centos7 kernel: ata23: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5eeb00 irq 56
Mar 31 04:37:58 centos7 kernel: ata24: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5eeb80 irq 56
Mar 31 04:37:58 centos7 kernel: ata25: SATA max UDMA/133 abar m4096@0xfd5ee000 port 0xfd5eec00 irq 56
~ 중략
```

> **sdiff 명령어**는 두 파일을 비교 할수 있는 명령어 입니다.  

```no-highlight
[root@linux ~]# sdiff file file1
test                                                            test
test                                                            test
test                                                            test
test                                                            test
test                                                            test
test                                                            test
test                                                            test
test                                                          | file
test                                                          | file
test                                                          | file
test                                                          | file
test1                                                         | file
test1                                                         | file
test1                                                         | file
test1                                                         | file
test1                                                         | file
[root@linux ~]#
```

> **tail 명령어**는 파일의 마지막 부분을 출력하는 명령어로 /var/log/messages 나 log 를 live 하게 확인 하기 위하여 사용 합니다.  

```no-highlight
[root@linux log]# tail -f /var/log/messages
```




## 네트워크  

> **ip 명령어**는 ip , route 를 확인,설정 할수 있는 명령어 입니다.  
```no-highlight

# Nic Device ip 를 확인 합니다.  
[root@linux ~]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:a5:b2:43 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.10/24 brd 192.168.0.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fea5:b243/64 scope link
       valid_lft forever preferred_lft forever
3: ens38: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:0c:29:a5:b2:4d brd ff:ff:ff:ff:ff:ff
[root@linux ~]#


# ens38 Nic 192.168.0.20/24 를 설정 합니다.  
[root@linux ~]# ip addr add 192.168.0.20/24 dev ens38


# ens38 Nic 192.168.0.20/24 를 삭제 합니다.  
[root@linux ~]# ip addr del 192.168.0.20/24 dev ens38


# ens38 Nic Device 를 활성화 합니다.  
[root@linux ~]# ip link set ens38 up


# ens38 Nic Device 를 비활성화 합니다.  
[root@linux ~]# ip link set ens38 down


# route 정보를 출력 합니다.  
[root@linux ~]# ip route show
default via 192.168.0.2 dev ens33
169.254.0.0/16 dev ens33 scope link metric 1002
192.168.0.0/24 dev ens33 proto kernel scope link src 192.168.0.10
[root@linux ~]#
```
> **traceroute 명령어**는 네트워크 경로 확인시 사용 합니다.  
> traceroute 패키지가 필요 하며, CentOS 의 경우 yum install traceroute 로 설치 할수 있습니다.  

```no-highlight
[root@linux ~]# traceroute google.com
traceroute to google.com (216.58.197.174), 30 hops max, 60 byte packets
 1  gateway (192.168.0.10)  3.481 ms  3.419 ms  3.369 ms
 2  118.33.9.73 (118.33.9.73)  2.872 ms  2.058 ms  3.426 ms
 3  112.188.58.37 (112.188.58.37)  1.305 ms  1.251 ms  1.198 ms
 4  112.188.52.33 (112.188.52.33)  9.892 ms  9.850 ms  9.797 ms
 5  * * *
 6  112.174.5.174 (112.174.5.174)  0.923 ms 112.174.7.14 (112.174.7.14)  1.143 ms 112.174.5.162 (112.174.5.162)  1.271 ms
 7  72.14.209.154 (72.14.209.154)  28.493 ms  28.423 ms  28.956 ms
 8  * * *
 9  108.170.242.193 (108.170.242.193)  37.255 ms 209.85.253.58 (209.85.253.58)  38.073 ms 209.85.242.44 (209.85.242.44)  28.193 ms
10  216.239.62.29 (216.239.62.29)  28.347 ms 108.170.242.209 (108.170.242.209)  30.932 ms 216.239.62.29 (216.239.62.29)  30.382 ms
11  nrt12s02-in-f174.1e100.net (216.58.197.174)  30.153 ms 72.14.234.67 (72.14.234.67)  28.760 ms nrt12s02-in-f174.1e100.net (216.58.197.174)  30.779 ms
[root@linux ~]#

```


> **ifconfig 명령어** 의 경우 Nic 정보 확인 및 설정시 사용 합니다, CentOS 7 Version 부터는 ip 사용을 권장 하고 있지만, 편의상 ifconfig 를 더 많이 사용 하는거 같습니다.  
net-tools 패키지가 필요 하며, CentOS 의 경우 yum install net-tools 명령어로 설치 합니다.  

```no-highlight
# 활성화된 Nic 확인  
[root@linux ~]# ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.10  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::20c:29ff:fea5:b243  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:a5:b2:43  txqueuelen 1000  (Ethernet)
        RX packets 1041  bytes 95669 (93.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 853  bytes 128277 (125.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@linux ~]#


# 모든 Nic 확인  
[root@linux ~]# ifconfig -a
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.10  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::20c:29ff:fea5:b243  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:a5:b2:43  txqueuelen 1000  (Ethernet)
        RX packets 1064  bytes 97777 (95.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 867  bytes 130651 (127.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens38: flags=4098<BROADCAST,MULTICAST>  mtu 1500
        ether 00:0c:29:a5:b2:4d  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 656 (656.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@linux ~]#


# ens38 Nic 192.168.0.20 netmask 255.255.255.0 설정  
[root@linux ~]# ifconfig ens38 192.168.0.20 netmask 255.255.255.0


# ens38 Nic link down
[root@linux ~]# ifconfig ens38 down


# ens38 Nic link up  
[root@linux ~]# ifconfig ens38 up

```

> **route 명령어**는 route 정보 확인 및 설정시 사용 합니다.  
```no-highlight
# Default Gateway 확인  
[root@linux ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.2     0.0.0.0         UG    0      0        0 ens33
169.254.0.0     0.0.0.0         255.255.0.0     U     1002   0        0 ens33
192.168.0.0     0.0.0.0         255.255.255.0   U     0      0        0 ens33
192.168.0.0     0.0.0.0         255.255.255.0   U     0      0        0 ens38
[root@linux ~]#


# Default Gateway 추가  
[root@linux ~]# route add default gw 192.168.0.2


# Default Gateway 삭제  
[root@linux ~]# route del default gw 192.168.0.2


# Route 경로 추가  
[root@linux ~]# route add -net 192.168.10.0 netmask 255.255.255.0 gw 192.168.10.2


# Route 경로 삭제  
[root@linux ~]# route del -net 192.168.10.0 netmask 255.255.255.0 gw 192.168.10.2
```

> **netstat 명령어**는 시스템에서 사용중인 network 정보를 확인 할수 있습니다.  

|옵션   |설명   |
|---|---|
|-a   |모든 소켓 경로테이블, 네트워크 인터페이스 정보까지 표시 합니다.   |
|-i   |네트워크 인터페이스 접속상태에 관한 정보를 표시 합니다.    |
|-r   |경로테이블(라우팅테이블)을 출력 합니다.  |
|-n   |네트워크 어드레스를 숫자로 표시 합니다.   |
|-t   |tcp 옵션으로 tcp 정보를 출력합니다.   |
|-u   |udp 옵션으로 udp 정보를 출력합니다.   |
|-p   |사용하고 있는 Program 및 port 정보를 출력합니다.   |
|-s   |각 프로토콜의 통계정보를 확인할수 있습니다.   |

```no-highlight
[root@linux ~]# netstat -antp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1728/master
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1089/sshd
tcp        0     64 192.168.0.10:22         192.168.0.1:6617        ESTABLISHED 1991/sshd: root@pts
tcp6       0      0 ::1:25                  :::*                    LISTEN      1728/master
tcp6       0      0 :::443                  :::*                    LISTEN      1088/httpd
tcp6       0      0 :::3306                 :::*                    LISTEN      1142/mysqld
tcp6       0      0 :::80                   :::*                    LISTEN      1088/httpd
tcp6       0      0 :::22                   :::*                    LISTEN      1089/sshd
[root@linux ~]#


# -s 옵션 사용시
[root@linux ~]# netstat -s
Ip:
    5598 total packets received
    0 forwarded
    0 incoming packets discarded
    5508 incoming packets delivered
    3593 requests sent out
Icmp:
    37 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 22
        timeout in transit: 15
    22 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 22
IcmpMsg:
        InType3: 22
        InType11: 15
        OutType3: 22
Tcp:
    0 active connections openings
    1 passive connection openings
    0 failed connection attempts
    0 connection resets received
    1 connections established
    5325 segments received
    3343 segments send out
    0 segments retransmited
    0 bad segments received.
    0 resets sent
Udp:
    28 packets received
    22 packets to unknown port received.
    0 packet receive errors
    273 packets sent
    0 receive buffer errors
    0 send buffer errors
UdpLite:
TcpExt:
    3 delayed acks sent
    1 packets directly queued to recvmsg prequeue.
    1959 packet headers predicted
    838 acknowledgments not containing data payload received
    1974 predicted acknowledgments
    IPReversePathFilter: 84
    TCPRcvCoalesce: 21
    TCPOrigDataSent: 3305
IpExt:
    InBcastPkts: 102
    InOctets: 448846
    OutOctets: 600311
    InBcastOctets: 7956
    InNoECTPkts: 5598
[root@linux ~]# 
```



> **ethtool 명령어**는 Nic 드라이버 및 하드웨어 설정을 출력합니다.   

```no-highlight
# ens33 Nic 정보 확인  
[root@linux ~]# ethtool ens33
Settings for ens33:
        Supported ports: [ TP ]
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        Supported pause frame use: No
        Supports auto-negotiation: Yes
        Supported FEC modes: Not reported
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
        Advertised pause frame use: No
        Advertised auto-negotiation: Yes
        Advertised FEC modes: Not reported
        Speed: 1000Mb/s                                             <-- Nic Speed 확인
        Duplex: Full                                                <-- Duplex 상태 확인
        Port: Twisted Pair
        PHYAD: 0
        Transceiver: internal
        Auto-negotiation: on
        MDI-X: off (auto)
        Supports Wake-on: d
        Wake-on: d
        Current message level: 0x00000007 (7)
                               drv probe link
        Link detected: yes                                          <-- 링크 활성화 상태 확인
[root@linux ~]#

# -i 옵션은 --driver 옵션으로 driver 정보를 확인할수 있습니다.  
[root@linux ~]# ethtool -i ens33
driver: e1000
version: 7.3.21-k8-NAPI
firmware-version:
expansion-rom-version:
bus-info: 0000:02:01.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: no
[root@linux ~]#


# Nic 로 신호를 보내서 Nic Device 의 물리적인 위치를 확인할때 사용 합니다.  
[root@linux ~]# ethtool -p ens33


```




## 시스템

> **ssh 명령어**는 OpenSSH SSH client 명령어로 ssh server 접속시 접속할때 사용 합니다.  
설정 파일은 /etc/ssh/ssh_config 에 있으며 패키지는 openssh-clients 패키지가 필요 합니다.  

```no-highlight
# 사용중인 계정으로 로그인시  
[root@linux ~]# ssh 192.168.0.20

# user01 계정으로 로그인  
[root@linux ~]# ssh user01@192.168.0.10

# 20202 포트를 사용 하여 user01 계정으로 로그인  
[root@linux ~]# ssh -p 20202 user01@192.168.0.20
```

> **top 명령어**는 프로세스 정보를 실시간으로 확인할때 사용 합니다.  

|옵션   |설명   |
|---|---|
|-i   |아이들 프로세스와 좀비 프로세시를 표시 하지 않습니다.   |
|-c   |명령어 및 옵션/인수 포함 명령어라인을 표시 합니다.   |
|-q   |갱신 간격을 최소로 설정 합니다.   |
|-d time   |갱신 간격을 time 으로 설정 합니다.   |
|-p pid   |프로세스 ID가 PID 프로세스만 표시 합니다.   |
|-n num   |num 회수 만큼 갱신하고 종료 합니다.   |

```no-highlight
[root@linux ~]#
top - 07:25:51 up 3 min,  1 user,  load average: 0.04, 0.09, 0.05
Tasks: 190 total,   2 running, 188 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us, 11.8 sy,  0.0 ni, 88.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3861336 total,  3340720 free,   342880 used,   177736 buff/cache
KiB Swap:  1048572 total,  1048572 free,        0 used.  3301348 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
  2048 root      20   0  162020   2228   1520 R  5.9  0.1   0:00.02 top
     1 root      20   0   46036   6540   4204 S  0.0  0.2   0:00.94 systemd
     2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
     3 root      20   0       0      0      0 S  0.0  0.0   0:00.50 kworker/0:0
     4 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:+
     5 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kworker/u2+
     6 root      20   0       0      0      0 S  0.0  0.0   0:00.04 ksoftirqd/0
     7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
     8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
     9 root      20   0       0      0      0 R  0.0  0.0   0:00.22 rcu_sched
    10 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 lru-add-dr+
    11 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 watchdog/0
    13 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kdevtmpfs
    14 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 netns
    15 root      20   0       0      0      0 S  0.0  0.0   0:00.00 khungtaskd
    16 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 writeback
    17 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kintegrityd
```
> **free 명령어**는 시스템에서 사용된 메모리를 확인할때 사용 합니다.  

|옵션   |설명   |
|---|---|
|-m   |M단위로 출력 합니다.   |
|-t   |total 값을 출력 합니다.   |
|-s   |N초 마다 출력 합니다.   |

```no-highlight
[root@linux ~]# free
              total        used        free      shared  buff/cache   available
Mem:        3861336      342508     3341012       16256      177816     3301664
Swap:       1048572           0     1048572
[root@linux ~]#

[root@linux ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           3770         333        3263          15         173        3224
Swap:          1023           0        1023

[root@linux ~]# free -t
              total        used        free      shared  buff/cache   available
Mem:        3861336      341912     3341588       16256      177836     3302252
Swap:       1048572           0     1048572
Total:      4909908      341912     4390160
[root@linux ~]#
```
> **sar 명령어**는 sysstat 에 포함된 명령어로 시스템의 활동 정보를 확인할수 있습니다.  
> sysstat daemon 이 활성화 되어 있으면 /var/log/sa 디렉토리에 10분에 한번씩 시스템 활동 정보를 생성 합니다.  
> sar 명령어는 sysstat 패키지가 필요 하며, CentOS 의 경우 yum install sysstat 명령어로 설치 할수 있습니다.

|옵션   |설명   |
|---|---|
|-A   |모든 정보를 출력합니다.   |
|-b   |버퍼 activity를 점검 i/o 와 transfer 의 통계를 출력합니다.   |
|-B   |페이징 통계를 출력합니다.   |
|-w   |새로 만들어져 활동하고 있는 프로세스를 출력합니다.   |
|-r   |가용메모리 점검 및 메모리 공간의 통계를 출력합니다.   |
|-R   |메모리 통계를 출력합니다.   |
|-S   |스왑 공간의 통계를 출력 합니다.   |

```no-highlight
# 1초 간격으로 100회 실행
[root@linux ~]# sar 1 100
Linux 3.10.0-1062.el7.x86_64 (linux)    04/02/2020      _x86_64_        (1 CPU)

07:38:47 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
07:38:48 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:49 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:50 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:51 AM     all      0.00      0.00      0.99      0.00      0.00     99.01
07:38:52 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:53 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:54 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
07:38:55 AM     all      0.00      0.00      1.00      0.00      0.00     99.00
^C

07:38:56 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
Average:        all      0.00      0.00      0.23      0.00      0.00     99.77
[root@linux ~]#


# 메모리 점검
[root@linux ~]# sar -r 1 100
Linux 3.10.0-1062.el7.x86_64 (linux)    04/02/2020      _x86_64_        (1 CPU)

07:39:55 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
07:39:56 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232160    123872         0
07:39:57 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232164    123872         0
07:39:58 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232172    123872         0
07:39:59 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232172    123872         0
07:40:00 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232172    123872         0
07:40:01 AM   3338228    523108     13.55      2076    155316   3074264     62.61    232172    123872         0
07:40:02 AM   3337696    523640     13.56      2076    155316   3074232     62.61    232388    123656         0
^C

07:40:02 AM   3337732    523604     13.56      2076    155320   3074232     62.61    232436    123612        20
Average:      3338100    523236     13.55      2076    155316   3074256     62.61    232230    123812         2
[root@linux ~]#


# sar file 을 이용한 확인 방법  
[root@linux sa]# sar -r -f sa02
Linux 3.10.0-1062.el7.x86_64 (linux)    04/02/2020      _x86_64_        (1 CPU)

07:22:28 AM       LINUX RESTART

07:30:01 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
07:40:01 AM   3336736    524600     13.59      2076    155316   3076556     62.66    232744    123872         0
Average:      3336736    524600     13.59      2076    155316   3076556     62.66    232744    123872         0
[root@linux sa]#


# 특정시간을 지정하여 확인
-s 시작 시간
-e 끝나는 시간 

[root@linux sa]# sar -r -f sa02 -s "07:00:00" -e "08:00:00"
Linux 3.10.0-1062.el7.x86_64 (linux)    04/02/2020      _x86_64_        (1 CPU)

07:22:28 AM       LINUX RESTART

07:30:01 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
07:40:01 AM   3336736    524600     13.59      2076    155316   3076556     62.66    232744    123872         0
Average:      3336736    524600     13.59      2076    155316   3076556     62.66    232744    123872         0
[root@linux sa]#
```

> **ps 명령어**는 프로세스 정보를 확인할때 사용합니다.  

|옵션   |설명   |
|---|---|
|-a   |터미널상 존재하는 모든 유저의 프로세스를 출력합니다.   |
|-e   |현재 실행중인 모든 프로세스를 출력합니다.   |
|-j   |유저명, 부모 프로세스 정보를 출력합니다.   |
|-u   |실제 유저명과 개시 시각을 출력합니다.   |
|-f   |full-format 으로 출력합니다.   |
|-x   |제어터미널에 없는 프로세스 정보를 출력합니다.   |

```no-highlight
# -ef 옵션을 사용 하여 실행중인 모든 프로세스와 full-format 출력  
[root@linux ~]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 07:22 ?        00:00:01 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2      0  0 07:22 ?        00:00:00 [kthreadd]
root          4      2  0 07:22 ?        00:00:00 [kworker/0:0H]
root          6      2  0 07:22 ?        00:00:00 [ksoftirqd/0]
root          7      2  0 07:22 ?        00:00:00 [migration/0]
root          8      2  0 07:22 ?        00:00:00 [rcu_bh]
root          9      2  0 07:22 ?        00:00:00 [rcu_sched]
root         10      2  0 07:22 ?        00:00:00 [lru-add-drain]
root         11      2  0 07:22 ?        00:00:00 [watchdog/0]
root         13      2  0 07:22 ?        00:00:00 [kdevtmpfs]
root         14      2  0 07:22 ?        00:00:00 [netns]
root         15      2  0 07:22 ?        00:00:00 [khungtaskd]
root         16      2  0 07:22 ?        00:00:00 [writeback]
root         17      2  0 07:22 ?        00:00:00 [kintegrityd]
root         18      2  0 07:22 ?        00:00:00 [bioset]
root         19      2  0 07:22 ?        00:00:00 [bioset]
root         20      2  0 07:22 ?        00:00:00 [bioset]
root         21      2  0 07:22 ?        00:00:00 [kblockd]
root         22      2  0 07:22 ?        00:00:00 [md]
root         23      2  0 07:22 ?        00:00:00 [edac-poller]
root         24      2  0 07:22 ?        00:00:00 [watchdogd]
root         30      2  0 07:22 ?        00:00:00 [kswapd0]
root         31      2  0 07:22 ?        00:00:00 [ksmd]
root         32      2  0 07:22 ?        00:00:00 [khugepaged]
root         33      2  0 07:22 ?        00:00:00 [crypto]
root         41      2  0 07:22 ?        00:00:00 [kthrotld]
root         43      2  0 07:22 ?        00:00:00 [kmpath_rdacd]
root         44      2  0 07:22 ?        00:00:00 [kaluad]
root         45      2  0 07:22 ?        00:00:00 [kpsmoused]
root         47      2  0 07:22 ?        00:00:00 [ipv6_addrconf]
root         60      2  0 07:22 ?        00:00:00 [deferwq]
root         95      2  0 07:22 ?        00:00:00 [kauditd]
root        233      2  0 07:22 ?        00:00:00 [nfit]
root        234      2  0 07:22 ?        00:00:00 [mpt_poll_0]
root        235      2  0 07:22 ?        00:00:00 [mpt/0]
root        236      2  0 07:22 ?        00:00:00 [ata_sff]
root        258      2  0 07:22 ?        00:00:00 [scsi_eh_0]
root        262      2  0 07:22 ?        00:00:00 [scsi_tmf_0]
root        285      2  0 07:22 ?        00:00:00 [scsi_eh_1]
root        290      2  0 07:22 ?        00:00:00 [scsi_tmf_1]
root        295      2  0 07:22 ?        00:00:00 [scsi_eh_2]
root        300      2  0 07:22 ?        00:00:00 [scsi_tmf_2]
root        310      2  0 07:22 ?        00:00:00 [irq/16-vmwgfx]
root        313      2  0 07:22 ?        00:00:00 [ttm_swap]
root        331      2  0 07:22 ?        00:00:00 [scsi_eh_3]
root        332      2  0 07:22 ?        00:00:00 [scsi_tmf_3]
root        333      2  0 07:22 ?        00:00:00 [scsi_eh_4]
root        334      2  0 07:22 ?        00:00:00 [scsi_tmf_4]
root        335      2  0 07:22 ?        00:00:00 [scsi_eh_5]
root        336      2  0 07:22 ?        00:00:00 [scsi_tmf_5]
root        337      2  0 07:22 ?        00:00:00 [scsi_eh_6]
root        338      2  0 07:22 ?        00:00:00 [scsi_tmf_6]
root        339      2  0 07:22 ?        00:00:00 [scsi_eh_7]
root        340      2  0 07:22 ?        00:00:00 [scsi_tmf_7]
~ 중략

# -aux 옵션 사용  
[root@linux ~]# ps -aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.1  46036  6548 ?        Ss   07:22   0:01 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2  0.0  0.0      0     0 ?        S    07:22   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        S<   07:22   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        S    07:22   0:00 [ksoftirqd/0]
root          7  0.0  0.0      0     0 ?        S    07:22   0:00 [migration/0]
root          8  0.0  0.0      0     0 ?        S    07:22   0:00 [rcu_bh]
root          9  0.0  0.0      0     0 ?        R    07:22   0:00 [rcu_sched]
root         10  0.0  0.0      0     0 ?        S<   07:22   0:00 [lru-add-drain]
root         11  0.0  0.0      0     0 ?        S    07:22   0:00 [watchdog/0]
root         13  0.0  0.0      0     0 ?        S    07:22   0:00 [kdevtmpfs]
root         14  0.0  0.0      0     0 ?        S<   07:22   0:00 [netns]
root         15  0.0  0.0      0     0 ?        S    07:22   0:00 [khungtaskd]
root         16  0.0  0.0      0     0 ?        S<   07:22   0:00 [writeback]
root         17  0.0  0.0      0     0 ?        S<   07:22   0:00 [kintegrityd]
root         18  0.0  0.0      0     0 ?        S<   07:22   0:00 [bioset]
root         19  0.0  0.0      0     0 ?        S<   07:22   0:00 [bioset]
root         20  0.0  0.0      0     0 ?        S<   07:22   0:00 [bioset]
root         21  0.0  0.0      0     0 ?        S<   07:22   0:00 [kblockd]
root         22  0.0  0.0      0     0 ?        S<   07:22   0:00 [md]
root         23  0.0  0.0      0     0 ?        S<   07:22   0:00 [edac-poller]
~ 중략


#현재 각 프로세스별 사용중인 메모리량
[root@linux ~]# ps -eo rss,pid,user,command --sort -rss
  RSS    PID USER     COMMAND
120456  1183 mysql    /usr/sbin/mysqld
29476    770 root     /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid
22872   1124 root     /usr/sbin/httpd -DFOREGROUND
17416   1122 root     /usr/bin/python2 -Es /usr/sbin/tuned -l -P
12952    743 polkitd  /usr/lib/polkit-1/polkitd --no-debug
12740   1291 apache   /usr/sbin/httpd -DFOREGROUND
12736   1308 apache   /usr/sbin/httpd -DFOREGROUND
12736   1309 apache   /usr/sbin/httpd -DFOREGROUND
12736   1310 apache   /usr/sbin/httpd -DFOREGROUND
12736   1353 apache   /usr/sbin/httpd -DFOREGROUND
12736   1390 apache   /usr/sbin/httpd -DFOREGROUND
12732   1292 apache   /usr/sbin/httpd -DFOREGROUND
 6548      1 root     /usr/lib/systemd/systemd --switched-root --system --deserialize 22
 6380    739 root     /usr/bin/vmtoolsd
 6060    738 root     /usr/bin/VGAuthService -s
 6032   2029 root     sshd: root@pts/0
 5348    544 root     /usr/lib/systemd/systemd-udevd
 4380   1126 root     /usr/sbin/rsyslogd -n
 4316   1123 root     /usr/sbin/sshd -D
 4252   1210 apache   /usr/sbin/httpd -DFOREGROUND
 4184   1753 postfix  qmgr -l -t unix -u
 4160   1752 postfix  pickup -l -t unix -u
 2964    516 root     /usr/lib/systemd/systemd-journald
 2452    745 dbus     /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation
 2200   2033 root     -bash
 2172   1747 root     /usr/libexec/postfix/master -w
 1992   2217 root     ps -eo rss,pid,user,command --sort -rss
 1744    736 root     /usr/lib/systemd/systemd-logind
 1672    761 root     /usr/sbin/crond -n
  888    714 root     /sbin/auditd
  856    765 root     /sbin/agetty --noclear tty1 linux
  768   2184 root     /usr/sbin/anacron -s
~ 중략


#현재 각 프로세스별 swap 사용량
[root@linux ~]# for i in `find /proc -maxdepth 2 -name status`;   do awk 'BEGIN {name=""} /Name/ {name=$NF} /VmSwap/ {sum+=$(NF-1)} END {if(sum) printf("%-25s - %d\n", name, sum)}' $i 2>/dev/null ; done | sort -rnk3


#프로세스 별 메모리사용량을 2초마다 한번씩 체크
[root@linux ~]# while : ;do ps -eo rss,pid,user,command --sort rss; sleep 2; done;
2964    516 root     /usr/lib/systemd/systemd-journald
 4160   1752 postfix  pickup -l -t unix -u
 4184   1753 postfix  qmgr -l -t unix -u
 4252   1210 apache   /usr/sbin/httpd -DFOREGROUND
 4316   1123 root     /usr/sbin/sshd -D
 4380   1126 root     /usr/sbin/rsyslogd -n
 5348    544 root     /usr/lib/systemd/systemd-udevd
 6032   2029 root     sshd: root@pts/0
 6060    738 root     /usr/bin/VGAuthService -s
 6380    739 root     /usr/bin/vmtoolsd
 6548      1 root     /usr/lib/systemd/systemd --switched-root --system --deserialize 22
12732   1292 apache   /usr/sbin/httpd -DFOREGROUND
12736   1308 apache   /usr/sbin/httpd -DFOREGROUND
12736   1309 apache   /usr/sbin/httpd -DFOREGROUND
12736   1310 apache   /usr/sbin/httpd -DFOREGROUND
12736   1353 apache   /usr/sbin/httpd -DFOREGROUND
12736   1390 apache   /usr/sbin/httpd -DFOREGROUND
12740   1291 apache   /usr/sbin/httpd -DFOREGROUND
12952    743 polkitd  /usr/lib/polkit-1/polkitd --no-debug
17416   1122 root     /usr/bin/python2 -Es /usr/sbin/tuned -l -P
22872   1124 root     /usr/sbin/httpd -DFOREGROUND
29476    770 root     /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid
120456  1183 mysql    /usr/sbin/mysqld
~중략
```
> **kill 명령어**는 프로세스 종료시 사용합니다.  
> -9 KILL 시그널을 사용 pid 값 으로 사용 합니다.  

```no-highlight
[root@linux ~]# ps -ef |grep -i top
root       2651   2636  0 08:14 pts/1    00:00:00 top
root       2653   2033  0 08:14 pts/0    00:00:00 grep --color=auto -i top
[root@linux ~]# kill -9 2651

```

> **lsof 명령어**는 열린 파일 목록을 확인 하기 위하여 사용 합니다.  

```no-highlight
#특정 ip에 대한 접속확인시 (192.168.0.1 은 ssh-client입니다.) 
[root@linux ~]# lsof -i@192.168.0.1
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    2035 root    3u  IPv4  24226      0t0  TCP linux:ssh->192.168.0.1:4718 (ESTABLISHED)
sshd    2221 root    3u  IPv4  29028      0t0  TCP linux:ssh->192.168.0.1:apc-5455 (ESTABLISHED)
[root@linux ~]#

# 소켓 확인시  
[root@linux ~]# lsof -i
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    1124   root    3u  IPv4  20132      0t0  TCP *:ssh (LISTEN)
sshd    1124   root    4u  IPv6  20141      0t0  TCP *:ssh (LISTEN)
httpd   1129   root    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1129   root    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
mysqld  1174  mysql   17u  IPv6  21239      0t0  TCP *:mysql (LISTEN)
httpd   1282 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1282 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1284 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1284 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1319 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1319 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1359 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1359 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1467 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1467 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1511 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1511 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
httpd   1512 apache    4u  IPv6  20483      0t0  TCP *:http (LISTEN)
httpd   1512 apache    6u  IPv6  20492      0t0  TCP *:https (LISTEN)
master  1724   root   13u  IPv4  20985      0t0  TCP localhost:smtp (LISTEN)
master  1724   root   14u  IPv6  20986      0t0  TCP localhost:smtp (LISTEN)
sshd    2035   root    3u  IPv4  24226      0t0  TCP linux:ssh->192.168.0.1:4718 (ESTABLISHED)
sshd    2221   root    3u  IPv4  29028      0t0  TCP linux:ssh->192.168.0.1:apc-5455 (ESTABLISHED)
[root@linux ~]#


# 특정 포트 접속 확인시  
[root@linux mnt]# lsof -i@192.168.0.10:22
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    2035 root    3u  IPv4  24226      0t0  TCP linux:ssh->192.168.0.1:4718 (ESTABLISHED)
sshd    2221 root    3u  IPv4  29028      0t0  TCP linux:ssh->192.168.0.1:apc-5455 (ESTABLISHED)
[root@linux mnt]#


#특정 디렉토리를 사용하는 유저 및 PID 확인  
[root@linux ~]# lsof +D /mnt
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
bash    2037 root  cwd    DIR   11,0     2048 1856 /mnt
[root@linux ~]#


# -c 프로세스로 열린 모든 파일을 나열 합니다.  
[root@linux ~]# lsof -c httpd
COMMAND  PID   USER   FD      TYPE             DEVICE SIZE/OFF    NODE NAME
httpd   1129   root  cwd       DIR                8,3      275      64 /
httpd   1129   root  rtd       DIR                8,3      275      64 /
httpd   1129   root  txt       REG                8,3   523680    1844 /usr/sbin/httpd
httpd   1129   root  DEL       REG                0,4            20681 /dev/zero
httpd   1129   root  DEL       REG                0,4            20678 /dev/zero
httpd   1129   root  mem       REG                8,3    84336  809818 /usr/lib64/php-zts/modules/xmlrpc.so
httpd   1129   root  mem       REG                8,3    33000  809510 /usr/lib64/php-zts/modules/xmlreader.so
httpd   1129   root  mem       REG                8,3    32736  809508 /usr/lib64/php-zts/modules/wddx.so
httpd   1129   root  mem       REG                8,3    29248  809504 /usr/lib64/php-zts/modules/pdo_sqlite.so
httpd   1129   root  mem       REG                8,3    29000  809781 /usr/lib64/php-zts/modules/pdo_odbc.so
httpd   1129   root  mem       REG                8,3    28848  809821 /usr/lib64/php-zts/modules/pdo_mysql.so
httpd   1129   root  mem       REG                8,3   137640  809819 /usr/lib64/php-zts/modules/mysqli.so
httpd   1129   root  mem       REG                8,3    19384   34847 /usr/lib64/libgpg-error.so.0.10.0
httpd   1129   root  mem       REG                8,3   535064   34883 /usr/lib64/libgcrypt.so.11.8.2
httpd   1129   root  mem       REG                8,3   258344   34931 /usr/lib64/libxslt.so.1.1.28
httpd   1129   root  mem       REG                8,3    87368   34929 /usr/lib64/libexslt.so.0.8.17
httpd   1129   root  mem       REG                8,3    32992  809512 /usr/lib64/php-zts/modules/xsl.so
httpd   1129   root  mem       REG                8,3    49240  809511 /usr/lib64/php-zts/modules/xmlwriter.so
httpd   1129   root  mem       REG                8,3    54400  809509 /usr/lib64/php-zts/modules/xml.so
httpd   1129   root  mem       REG                8,3    19920  809496 /usr/lib64/php-zts/modules/tokenizer.so
httpd   1129   root  mem       REG                8,3    15792  809527 /usr/lib64/php-zts/modules/sysvshm.so
httpd   1129   root  mem       REG                8,3    11568  809526 /usr/lib64/php-zts/modules/sysvsem.so
httpd   1129   root  mem       REG                8,3    15880  809525 /usr/lib64/php-zts/modules/sysvmsg.so
httpd   1129   root  mem       REG                8,3   753232   34906 /usr/lib64/libsqlite3.so.0.8.6
httpd   1129   root  mem       REG                8,3    55672  809505 /usr/lib64/php-zts/modules/sqlite3.so
httpd   1129   root  mem       REG                8,3    87600  809495 /usr/lib64/php-zts/modules/sockets.so
httpd   1129   root  mem       REG                8,3   319760  809824 /usr/lib64/php-zts/modules/soap.so
httpd   1129   root  mem       REG                8,3    58456  809507 /usr/lib64/php-zts/modules/simplexml.so
httpd   1129   root  mem       REG                8,3    15720  809524 /usr/lib64/php-zts/modules/shmop.so
httpd   1129   root  mem       REG                8,3    33120  809523 /usr/lib64/php-zts/modules/posix.so
httpd   1129   root  mem       REG                8,3   294760  809494 /usr/lib64/php-zts/modules/phar.so
httpd   1129   root  mem       REG                8,3   112448  809503 /usr/lib64/php-zts/modules/pdo.so
httpd   1129   root  mem       REG                8,3    41272   34939 /usr/lib64/libltdl.so.7.3.0
httpd   1129   root  mem       REG                8,3   429736  809729 /usr/lib64/libodbc.so.2.0.0
httpd   1129   root  mem       REG                8,3    70504  809721 /usr/lib64/php-zts/modules/odbc.so
httpd   1129   root  mem       REG                8,3   274944  809820 /usr/lib64/php-zts/modules/mysqlnd.so
httpd   1129   root  mem       REG                8,3  1445768  809825 /usr/lib64/php-zts/modules/mbstring.so
httpd   1129   root  mem       REG                8,3   381416  181904 /usr/lib64/libldap_r-2.4.so.2.10.7
httpd   1129   root  mem       REG                8,3    66240  809822 /usr/lib64/php-zts/modules/ldap.so
httpd   1129   root  mem       REG                8,3    40816  809484 /usr/lib64/php-zts/modules/json.so
~ 중략 
```
> **fuser 명령어**는 파일 또는 소켓을 사용하여 프로세스를 식별 할때 사용 합니다.  
> fuser 사용시 psmisc 패키지가 필요 합니다. CentOS 에서는 yum install psmisc 로 설치 할수 있습니다.  

```no-highlight
# /root 디렉토리 사용 User, PID, Command 확인  
[root@linux ~]# fuser -v .
                     USER        PID ACCESS COMMAND
/root:               root       2223 ..c.. sftp-server
                     root       2425 ..c.. bash
[root@linux ~]#


# user01 디렉토리 사용자 kill  
[root@linux ~]# fuser -k /home/user01
/home/user01:         2583c
[root@linux ~]#


# -ki 옵션 사용시 kill 시키기전 사용자에 확인  
[root@linux ~]# fuser -ki /home/user01
/home/user01:         2607c
Kill process 2607 ? (y/N)


# ssh 사용 유저 확인  
[root@linux ~]# fuser -vn tcp ssh
                     USER        PID ACCESS COMMAND
ssh/tcp:             root       1124 F.... sshd
                     root       2558 F.... sshd
                     root       2604 F.... sshd
                     user01     2606 F.... sshd
[root@linux ~]#


# ssh 사용 유저 kill 
[root@linux ~]# fuser -kn tcp ssh
```

> **crontab 명령어**는 crontab 작업을 설정 또는 확인시 사용 합니다.  

|옵션   |설명   |
|---|---|
|-e   |작업을 등록시 사용합니다.   |
|-l   |등록되어 있는 작업을 출력합니다.   |
|-r   |등록되어 있는 작업을 삭제 합니다.   |

```no-highlight
[root@linux ~]# crontab -l
no crontab for root
[root@linux ~]#

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed


*/10 * * * * /var/scripts/backup.sh
10분마다 backup.sh 실행

20,40 * * * * /var/scripts/backup.sh
매시 20분과 40분에 backup.sh 실행  

30 * * * * /var/scripts/backup.sh
30분 마다  backup.sh 실행

30 1 * * * /var/scripts/backup.sh
매일 새벽 1시 30분에 backup.sh 실행
```
> **uname 명령어**는 시스템 정보를 출력할때 사용합니다.

```no-highlight
[root@linux ~]# uname -a
Linux linux 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

> **hostname 명령어**는 hostname 확인시 사용합니다.  

```no-highlight
[root@linux ~]# hostname
linux
```

> **hostnamectl 명령어**는 hostname 설정시 사용합니다.  
> 터미널 재접속시 hostname 이 변경된 터미널로 접속 할수 있습니다.  

```no-highlight
[root@linux ~]# hostnamectl set-hostname linux7
[root@linux ~]# cat /etc/hostname
linux7


login as: root
root@192.168.0.10's password:
Last login: Thu Apr  2 08:14:30 2020 from 192.168.0.1
[root@linux7 ~]#
```

> **uptime 명령어**는 시스템 구동시간을 확인할때 사용합니다.   

```no-highlight
[root@linux ~]# uptime
10:38:57 up 34 min,  1 user,  load average: 0.00, 0.01, 0.05
```


> **date 명령어**는 시스템 시간을 표시/설정 할때 사용합니다.   
> 시스템 시간을 확인 할때 일반적으로 사용하며, 시스템 시간이 맞지 않을 경우 chronyd , ntp 를 이용하여 시간을 동기화 합니다.  

```no-highlight
[root@linux ~]# date
Wed Apr  1 23:30:49 KST 2020
```

> **history 명령어**는 명령어 history 를 확인할때 사용 합니다.  


```no-highlight
[root@linux ~]# history
    1  vi /etc/hostname
    2  cd
    3  cd /etc/sysconfig/network-scripts/
    4  ls
    5  vi ifcfg-ens33
    6  init 6
    7  vi /etc/selinux/config
    8  ls
    9  clear
   10  yum install httpd mod_ssl
   11  clear
   12  systemctl start httpd
   13  systemctl status httpd
   14  iptables -L
   15  clear     <-- !15 실행시 clear 실행  
   16  firewall-cmd --permanent --zone=public --add-service=http
   17  firewall-cmd --reload
   18  iptables -L
   19  firewall-cmd --permanent --zone=public --add-port=80/tcp
   20  ifco
   21  ip addr show
   22  route -n
   23  yum install net-tools
   24  vi /etc/resolv.conf
   25  yum install net-tools
   26  ifconfig
   27  init 0
   28  ifconfig
   29  init 0
   30  ifconfig
   31  ls
   32  ll
   33  mkdir sos
~중략  

# history 에 있는 명령어 를 실행시 !$history_number  
ex) !15 를 실행 하면 clear 가 실행 됩니다.  


# -c 옵션 사용시 history 를 초기화 할수 있습니다.  
[root@linux ~]# history -c
[root@linux ~]# history
    1  history
[root@linux ~]#

```

> **which 명령어**는 명령어 위치를 확인할때 사용 합니다.  

```no-highlight
[root@linux ~]# which rpm
/usr/bin/rpm
[root@linux ~]#
```

> **whereis 명령어**는 binary, 소스 파일, 매뉴얼 파일을 찾을때 사용합니다.  

```no-highlight
[root@linux ~]# whereis rpm
rpm: /usr/bin/rpm /usr/lib/rpm /etc/rpm /usr/share/man/man8/rpm.8.gz
[root@linux ~]#
```

> **dmidecode 명령어**는 DMI 테이블 내용을 사람이 읽을 수있는 형식으로 덤프하는 도구입니다.  
> 일반적으로 하드웨어 정보를 확인시 사용합니다.  

```no-highlight
[root@linux ~]# dmidecode -t
dmidecode: option requires an argument -- 't'
Type number or keyword expected
Valid type keywords are:
  bios
  system
  baseboard
  chassis
  processor
  memory
  cache
  connector
  slot
[root@linux ~]#

[root@linux ~]# dmidecode -t system
# dmidecode 3.2
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0001, DMI type 1, 27 bytes
System Information
        Manufacturer: VMware, Inc.
        Product Name: VMware Virtual Platform
        Version: None
        Serial Number: VMware-56 4d 17 83 79 92 52 5d-7e 16 32 9b 20 a5 b2 43
        UUID: 83174d56-9279-5d52-7e16-329b20a5b243
        Wake-up Type: Power Switch
        SKU Number: Not Specified
        Family: Not Specified

Handle 0x01A1, DMI type 15, 29 bytes
System Event Log
        Area Length: 16 bytes
        Header Start Offset: 0x0000
        Header Length: 16 bytes
        Data Start Offset: 0x0010
        Access Method: General-purpose non-volatile data functions
        Access Address: 0x0000
        Status: Invalid, Full
        Change Token: 0x00000036
        Header Format: Type 1
        Supported Log Type Descriptors: 3
        Descriptor 1: POST error
        Data Format 1: POST results bitmap
        Descriptor 2: Single-bit ECC memory error
        Data Format 2: Multiple-event
        Descriptor 3: Multi-bit ECC memory error
        Data Format 3: Multiple-event

Handle 0x0265, DMI type 23, 13 bytes
System Reset
        Status: Enabled
        Watchdog Timer: Present
        Boot Option: Do Not Reboot
        Boot Option On Limit: Do Not Reboot
        Reset Count: Unknown
        Reset Limit: Unknown
        Timer Interval: Unknown
        Timeout: Unknown

Handle 0x0268, DMI type 32, 20 bytes
System Boot Information
        Status: No errors detected

[root@linux ~]#
```

> **sosreport 명령어**는 시스템 진단 및 지원 데이터 수집할때 사용 하는 명령어 입니다.  
> RHEL 사용시 RedHat 기술지원시 필요 할수 있으며, CentOS 의 경우 엔지니어들이 장애 처리 할때 H/W 정보를 게더링 할때 사용 합니다.   
> sos 패키지가 필요 하며, CentOS 에서는 yum install sos 로 패키지를 설치 할수 있습니다.  

```no-highlight
[root@linux ~]# sosreport

sosreport (version 3.7)

This command will collect diagnostic and configuration information from
this CentOS Linux system and installed applications.

An archive containing the collected information will be generated in
/var/tmp/sos.r02arU and may be provided to a CentOS support
representative.

Any information provided to CentOS will be treated in accordance with
the published support policies at:

  https://wiki.centos.org/

The generated archive may contain data considered sensitive and its
content should be reviewed by the originating organization before being
passed to any third party.

No changes will be made to system configuration.

Press ENTER to continue, or CTRL-C to quit.

Please enter the case id that you are generating this report for []:

 Setting up archive ...
 Setting up plugins ...

 Running plugins. Please wait ...

  Finishing plugins              [Running: yum]
  Finished running plugins
Creating compressed archive...

Your sosreport has been generated and saved in:
  /var/tmp/sosreport-linux-2020-04-01-ibyjcst.tar.xz

The checksum is: 9ea7ae07238029cdd02e9d7d8c070860

Please send this file to your support representative.

[root@linux ~]#

# rpm 패키지 정보를 제외하고 시스템 정보를 수집 (rpm 패키지 정보가 들어가면 sosreport 생성이 오래 걸릴수 있습니다.)
[root@linux ~]# sosreport  -k rpm.rpmva=off


-l 옵션과 , -n 옵션도 상당히 유용하게 사용 할수 있습니다.  

       -l, --list-plugins
              List all available plugins and their options. Plug-ins that would not be enabled by the current configuration are listed separately.

       -n, --skip-plugins PLUGNAME[,PLUGNAME]
              Disable the specified plugin(s). Multiple plug-ins may be specified by repeating the option or as a comma-separated list.

```

> **sync 명령어**는  flush file system buffers로 buffer 에 있는 파일을  파일시스템에 기록합니다.  
> usb 파일복사후 umount 를 하였지만 usb 에 복사한 파일이 없을때가 있습니다.  

```no-highlight
[root@linux ~]# sync
[root@linux ~]# sync
[root@linux ~]# sync
```

> **init 명령어**는 시스템에서 사용하는 runlevel 명령어 입니다.  
> 일반적으로 init 0 시스템 종료, init 6 시스템 재시작 할때 많이 사용 합니다.  

|runlevel   |설명   |
|---|---|
|0   |halt   |
|1   |Single user mode   |
|2   |Multiuser, without NFS   |
|3   |Full multiuser mode   |
|4   |unused   |
|5   |X11   |
|6   |reboot   |

```no-highlight
# 시스템 종료
[root@linux ~]# init 0


# 시스템 재시작
[root@linux ~]# init 6
```



## 유저,구룹

> **su 명령어**는 root 유저 또는 다른 유저로 변경할때 사용 합니다.  

```no-highlight
# test 유저로 변경  
[root@linux ~]# su - test
[test@linux ~]$

# root 유저로 변경  
[test@linux ~]$ su -
Password:
Last login: Thu Apr  2 08:25:54 KST 2020 from 192.168.0.1 on pts/2
[root@linux ~]#
```

> **id 명령어**는 유저id와 그룹id 를 확인할때 사용 합니다.  

```no-highlight
[root@linux ~]# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[root@linux ~]#

[test@linux ~]$ id
uid=1000(test) gid=1000(test) groups=1000(test) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[test@linux ~]$
```

> **whoami 명령어**는 유저명 확인시 사용합니다.  

```no-highlight
[root@linux ~]# whoami
root


[test@linux ~]$ whoami
test
```

> **w 명령어**는 로그인 중인 유저를 확인할때 사용합니다.  

```no-highlight
[root@linux ~]# w
 00:17:45 up  1:55,  1 user,  load average: 0.01, 0.02, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/1    192.168.0.1      08:14     ?     0.17s  0.00s w
[root@linux ~]#
```


> **useradd 명령어**는 유저생성시 사용합니다.  

|옵션   |설명   |
|---|---|
|-c   |유저 comment를 작성 합니다.    |
|-d   |유저 홈디렉토리를 지정합니다.    |
|-u   |UID 를 지정 합니다.    |
|-g   |GID 를 지정 합니다.    |
|-s   |유저 로그인 shell 을 지정 합니다.    |
|-D   |useradd 설정값을 확인 합니다.   |

```no-highlight
#test 유저를 생성 합니다.  
[root@linux ~]# useradd test


# -D 옵션 사용시  
[root@linux ~]# useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
[root@linux ~]#

```

> **userdel 명령어**는 유저삭제시 사용합니다.  

```no-highlight
# userdel $User_name 명령어로는 home 디렉토리가 삭제 되지 않습니다.  
[root@linux ~]# userdel test
[root@linux ~]# ls -al /home
total 0
drwxr-xr-x.  4 root   root    32 Apr  2 00:29 .
dr-xr-xr-x. 19 root   root   255 Mar 31 07:10 ..
drwx------.  2   1002   1002  62 Apr  2 00:29 test
drwx------.  2 user01 user01  62 Mar 31 10:21 user01
[root@linux ~]#

# userdel -r $User_name 사용시 home 디렉토리까지 삭제 할수 있습니다.  
[root@linux ~]# userdel -r test
userdel: /var/spool/mail/test not owned by test, not removing
[root@linux ~]# ls -al /home
total 0
drwxr-xr-x.  3 root   root    20 Apr  2 00:28 .
dr-xr-xr-x. 19 root   root   255 Mar 31 07:10 ..
drwx------.  2 user01 user01  62 Mar 31 10:21 user01
[root@linux ~]#
```

> **usermod 명령어**는 유저 정보를 변경시 사용 합니다.  

```no-highlight
# test 유저 dba group 으로 지정  
[root@linux ~]# usermod -G dba test
[root@linux ~]# cat /etc/group|grep dba
dba:x:2000:test
```

> **groupadd 명령어**는 group 생성시 사용합니다.  

```no-highlight
[root@linux ~]# groupadd dba
[root@linux ~]# cat /etc/group|grep dba
dba:x:1002:

# GID 지정시 -g 옵션을 사용 합니다.
[root@linux ~]# groupadd -g 2000 dba
[root@linux ~]# cat /etc/group|grep dba
dba:x:2000:
```

> **groupdel 명령어**는 group 삭제시 사용합니다.  

```no-highlight
[root@linux ~]# groupdel dba
```

> **chown 명령어**는 파일의 소유자와 소속 구룹 변경시 사용합니다.  

```no-highlight
[root@linux ~]# ls -al /var/www/html/
total 4
drwxr-xr-x. 3 root   root    40 Apr  2 00:42 .
drwxr-xr-x. 4 root   root    33 Mar 26 08:44 ..
-rw-r--r--. 1 apache apache 141 Mar 27 07:52 index.html
drwxr-xr-x. 2 root   root     6 Apr  2 00:42 test.com
[root@linux ~]# 

[root@linux ~]# ls -al /var/www/html/test.com/
total 0
drwxr-xr-x. 2 apache apache 18 Apr  2 00:42 .
drwxr-xr-x. 3 root   root   40 Apr  2 00:42 ..
-rw-r--r--. 1 root   root    0 Apr  2 00:42 file


# -R 옵션사용시 디렉토리 하위 파일들 까지 변경이 됩니다.  
[root@linux ~]# chown -R apache:apache /var/www/html/
[root@linux ~]# ls -al /var/www/html/test.com/
total 0
drwxr-xr-x. 2 apache apache 18 Apr  2 00:42 .
drwxr-xr-x. 3 apache apache 40 Apr  2 00:42 ..
-rw-r--r--. 1 apache apache  0 Apr  2 00:42 file
[root@linux ~]#
```

> **chmod 명령어**는 파일모드 변경시 사용합니다.  

```no-highlight

#file 퍼미션 711 로 설정  
[root@linux ~]# ls -al /var/www/html/test.com/
total 0
drwxr-xr-x. 2 apache apache 18 Apr  2 00:42 .
drwxr-xr-x. 3 apache apache 40 Apr  2 00:42 ..
-rw-r--r--. 1 apache apache  0 Apr  2 00:42 file
[root@linux ~]# man chmod
[root@linux ~]# chmod 711 /var/www/html/test.com/file
[root@linux ~]# ls -al /var/www/html/test.com/
total 0
drwxr-xr-x. 2 apache apache 18 Apr  2 00:42 .
drwxr-xr-x. 3 apache apache 40 Apr  2 00:42 ..
-rwx--x--x. 1 apache apache  0 Apr  2 00:42 file
[root@linux ~]#

# -R 옵션사용시 디렉토리 하위 파일들 까지 766으로 변경  
[root@linux ~]# chmod -R 766 /var/www/html/test.com/
[root@linux ~]# ls -al /var/www/html/test.com/
total 0
drwxrw-rw-. 2 apache apache 18 Apr  2 00:42 .
drwxr-xr-x. 3 apache apache 40 Apr  2 00:42 ..
-rwxrw-rw-. 1 apache apache  0 Apr  2 00:42 file
[root@linux ~]#

```

> **passwd 명령어**는 패스워드 설정시 사용합니다.  
> root User 의 경우 다른 유저의 패스워드를 변경 할수 있으며, 일반 유저는 자기자신의 패스워드를 설정 할수 있습니다.  

```no-highlight
# root 유저로 test 유저 패스워드 변경  
[root@linux ~]# passwd test
Changing password for user test.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
[root@linux ~]#

# 일반유저의 패스워드 변경  
[test@linux ~]$ whoami
test
[test@linux ~]$ passwd
Changing password for user test.
Changing password for test.
(current) UNIX password:  <-- 기존 패스워드 입력  
New password:             <-- 신규 패스워드 입력
Retype new password:      <-- 신규 패스워드 입력  
passwd: all authentication tokens updated successfully.
[test@linux ~]$

```


## 파일시스템

> **df 명령어**는 파일시스템의 디스크 사용량을 확인시 사용합니다.  

|옵션   |설명   |
|---|---|
|-h   |human-readable 옵션으로 사람이 보고 이해하기 쉬운 형식으로 출력합니다.   |
|-k   |K바이트 단위로 출력합니다.   |
|-i   |inode 옵션으로 inode 정보를 출력합니다.   |

```no-highlight
[root@linux ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G   12M  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda3       8.0G  2.2G  5.9G  28% /
/dev/sda1      1014M  141M  874M  14% /boot
tmpfs           378M     0  378M   0% /run/user/0


[root@linux ~]# df -k
Filesystem     1K-blocks    Used Available Use% Mounted on
devtmpfs         1919928       0   1919928   0% /dev
tmpfs            1930668       0   1930668   0% /dev/shm
tmpfs            1930668   11812   1918856   1% /run
tmpfs            1930668       0   1930668   0% /sys/fs/cgroup
/dev/sda3        8377344 2277504   6099840  28% /
/dev/sda1        1038336  144308    894028  14% /boot
tmpfs             386136       0    386136   0% /run/user/0


[root@linux ~]# df -i
Filesystem      Inodes IUsed   IFree IUse% Mounted on
devtmpfs        479982   374  479608    1% /dev
tmpfs           482667     1  482666    1% /dev/shm
tmpfs           482667  1219  481448    1% /run
tmpfs           482667    16  482651    1% /sys/fs/cgroup
/dev/sda3      4193792 36815 4156977    1% /
/dev/sda1       524288   327  523961    1% /boot
tmpfs           482667     1  482666    1% /run/user/0
[root@linux ~]#

```

> **fdisk 명령어**는 파티션 설정시 사용하는 명령어 입니다.  
> 2T 이하의 Disk 만 작업 하며 2T 이상의 Disk 사용시 parted 를 사용 합니다.  

```no-highlight
[root@linux ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0    8G  0 part /
sdb      8:16   0   10G  0 disk
sr0     11:0    1  4.4G  0 rom
[root@linux ~]#


[root@linux ~]# fdisk -l

Disk /dev/sda: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009bd88

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200     4196351     1048576   82  Linux swap / Solaris
/dev/sda3         4196352    20971519     8387584   83  Linux

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors    <-- 사용하지 않는디스크 확인 
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

[root@linux ~]#



[root@linux ~]# cat /proc/partitions
major minor  #blocks  name

   8        0   10485760 sda
   8        1    1048576 sda1
   8        2    1048576 sda2
   8        3    8387584 sda3
   8       16   10485760 sdb
  11        0    4554752 sr0
[root@linux ~]#


# Fdisk 를 이용하여 디스크 파티셔닝 
(단일 파티션으로  100% 용량을 사용하여 디스크 생성)
[root@linux ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x7ef63b80.

Command (m for help): n                         <-- 새로운 파티션을 생성
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p                           <-- primary partition 으로 생성 
Partition number (1-4, default 1):
First sector (2048-20971519, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519):
Using default value 20971519
Partition 1 of type Linux and of size 10 GiB is set

Command (m for help): p                         <-- print 명령어를 사용하여 파티션 확인

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x7ef63b80

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    20971519    10484736   83  Linux


Command (m for help): w                         <-- write table 명령어를 사용하여 저장  
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@linux ~]#


[root@linux ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): h
h: unknown command
Command action
   a   toggle a bootable flag                         부트 가능한 플래그로 변경
   b   edit bsd disklabel                             bsd 디스크 레이블을 편집
   c   toggle the dos compatibility flag              도스 호환 플래그로 변경
   d   delete a partition                             파티션 삭제 
   g   create a new empty GPT partition table         GPT 파티션 테이블 생성
   G   create an IRIX (SGI) partition table           IRIX (SGI) 파티션 테이블 생성
   l   list known partition types                     알려진 파티션 형태의 목록
   m   print this menu                                이 메뉴를 출력
   n   add a new partition                            새로운 파티션을 생성
   o   create a new empty DOS partition table         새로운 도스 파티션 테이블을 생성
   p   print the partition table                      파티션 테이블 출력
   q   quit without saving changes                    변경을 저장하지 않고 종료
   s   create a new empty Sun disklabel               새로운 Sun 디스크 레이블을 생성
   t   change a partition's system id                 파티션의 시스템 id를 변경
   u   change display/entry units                     표시/엔트리 단위를 변경 
   v   verify the partition table                     파티션 테이블을 점검
   w   write table to disk and exit                   디스크에 테이블을 기록하고 빠져나감
   x   extra functionality (experts only)

Command (m for help):



# 파티션 용량을 지정하여 파티션 생성  
[root@linux ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n                                                                 <-- 새로운 파티션을 생성 
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p                                                                   <-- primary 파티션 생성 
Partition number (1-4, default 1): 1                                                    <-- 파티션 넘버를 입력 : 1번째 파티션 생성
First sector (2048-20971519, default 2048):                                             <-- 엔터를 입력 default 로 2048로 지정됩니다.
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): +1G            <-- 용량을 1G 로 설정 합니다. 
Partition 1 of type Linux and of size 1 GiB is set

Command (m for help): n                                                                 <-- 새로운 파티션을 생성 
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p                                                                   <-- primary 파티션 생성 
Partition number (2-4, default 2): 2                                                    <-- 파티션 넘버를 입력 : 2번째 파티션 생성
First sector (2099200-20971519, default 2099200):                                       <-- 엔터를 입력 default 로 2099200로 지정됩니다.
Using default value 2099200
Last sector, +sectors or +size{K,M,G} (2099200-20971519, default 20971519): +1G         <-- 용량을 1G 로 설정 합니다. 
Partition 2 of type Linux and of size 1 GiB is set

Command (m for help): n                                                                 <-- 새로운 파티션을 생성 
Partition type:
   p   primary (2 primary, 0 extended, 2 free)
   e   extended
Select (default p): p                                                                   <-- primary 파티션 생성 
Partition number (3,4, default 3): 3                                                    <-- 파티션 넘버를 입력 : 3번째 파티션 생성
First sector (4196352-20971519, default 4196352):                                       <-- 엔터를 입력 default 로 4196352로 지정됩니다.
Using default value 4196352
Last sector, +sectors or +size{K,M,G} (4196352-20971519, default 20971519): +1G         <-- 용량을 1G 로 설정 합니다. 
Partition 3 of type Linux and of size 1 GiB is set

Command (m for help): n                                                                 <-- 새로운 파티션을 생성 
Partition type:
   p   primary (3 primary, 0 extended, 1 free)
   e   extended
Select (default e): e                                                                   <-- extended 파티션으로 지정 
Selected partition 4
First sector (6293504-20971519, default 6293504):                                       <-- 엔터를 입력 default 로 6293504로 지정됩니다.
Using default value 6293504
Last sector, +sectors or +size{K,M,G} (6293504-20971519, default 20971519):             <-- 모든공간을 Extended 파티션으로 생성하기 위하여 엔터를 입력합니다. 
Using default value 20971519
Partition 4 of type Extended and of size 7 GiB is set

Command (m for help): p                                                                 <-- p 를 입력하여 파티션 테이블 출력

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x7ef63b80

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb2         2099200     4196351     1048576   83  Linux
/dev/sdb3         4196352     6293503     1048576   83  Linux
/dev/sdb4         6293504    20971519     7339008    5  Extended

Command (m for help): n                                                                 <-- 새로운 파티션을 생성합니다. 
새로운 파티션 생성시 Extended  의 용량을 사용하여 파티션생성이 가능합니다. 
Extended 파티션의 경우 12개 이상 생성이 가능하지만 cylinder 의 영향을 받습니다.
Extended 파티션은 12개 이상 생성시 성능이슈가 발생할수 있습니다.



All primary partitions are in use
Adding logical partition 5
First sector (6295552-20971519, default 6295552):
Using default value 6295552
Last sector, +sectors or +size{K,M,G} (6295552-20971519, default 20971519): +100M
Partition 5 of type Linux and of size 100 MiB is set

Command (m for help): n                                                                 <-- 새로운 파티션을 생성합니다. 
All primary partitions are in use
Adding logical partition 6
First sector (6502400-20971519, default 6502400):
Using default value 6502400
Last sector, +sectors or +size{K,M,G} (6502400-20971519, default 20971519): +100M
Partition 6 of type Linux and of size 100 MiB is set

Command (m for help): p                                                                 <-- p 를 입력하여 파티션 테이블 출력 

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x7ef63b80

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb2         2099200     4196351     1048576   83  Linux
/dev/sdb3         4196352     6293503     1048576   83  Linux
/dev/sdb4         6293504    20971519     7339008    5  Extended
/dev/sdb5         6295552     6500351      102400   83  Linux
/dev/sdb6         6502400     6707199      102400   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@linux ~]#

```

> **parted 명령어**는 파티션 작업시 사용되는 프로그램 입니다.  
> 2T 이상의 파티션 작업시 사용 합니다.  

```no-highlight
[root@linux ~]# parted /dev/sdb
GNU Parted 3.1
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B 
Partition Table: msdos                                                      <-- 라벨타입이 msdos 이며 2T 이상 사용시 gpt 로 변경 해야 합니다.
Disk Flags:

Number  Start  End  Size  Type  File system  Flags

(parted) mklabel gpt
Warning: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt                                                        <-- 라벨타입이 gtp 로 변경 되었습니다.  
Disk Flags:

Number  Start  End  Size  File system  Name  Flags

(parted) mkpart                                                             <-- mkpart 로 대화형으로 설정 할수 있습니다.
Partition name?  []?
File system type?  [ext2]? xfs
Start? 1
End? 1G
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size   File system  Name  Flags
 1      1049kB  1000MB  999MB

(parted) mkpart primary 1000 2000                                           <-- mkpart  용량지정으로 생성도 가능 합니다. 1000 ~ 2000 (약 1G)
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size   File system  Name     Flags
 1      1049kB  1000MB  999MB
 2      1000MB  2000MB  999MB               primary

(parted) mkpart primary 2000 100%                                           <-- mkpart  %지정으로 나머지 용량을 설정 할수 있습니다.
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  1000MB  999MB
 2      1000MB  2000MB  999MB                primary
 3      2000MB  10.7GB  8737MB               primary


(parted) set 1 lvm on                                                       <--- lvm 사용시
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 1      1049kB  1000MB  999MB                         lvm
 2      1000MB  2000MB  999MB                primary
 3      2000MB  10.7GB  8737MB               primary

(parted) rm 1                                                               <-- 파티션 삭제시 rm 명령어를 이용합니다.
(parted) p
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 10.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 2      1000MB  2000MB  999MB                primary
 3      2000MB  10.7GB  8737MB               primary

(parted) q
Information: You may need to update /etc/fstab.

[root@linux ~]# partprobe


[root@linux ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0    8G  0 part /
sdb      8:16   0   10G  0 disk
├─sdb2   8:18   0  953M  0 part
└─sdb3   8:19   0  8.1G  0 part
sr0     11:0    1  4.4G  0 rom
[root@linux ~]#
```

> **mount 명령어**는 파일시스템을 마운트 할때 사용 합니다.  
> cd-rom, disk, nfs Volume 등을 mount 할수 있습니다.  
> -a 옵션 사용시 /etc/fstab 설정된 filesystem을 전부 마운트 합니다.  


```no-highlight
# mount 정보 확인
[root@linux ~]# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
devtmpfs on /dev type devtmpfs (rw,nosuid,size=1919928k,nr_inodes=479982,mode=755)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpuacct,cpu)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_prio,net_cls)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
configfs on /sys/kernel/config type configfs (rw,relatime)
/dev/sda3 on / type xfs (rw,relatime,attr2,inode64,noquota)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=26,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=13835)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
mqueue on /dev/mqueue type mqueue (rw,relatime)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)
/dev/sda1 on /boot type xfs (rw,relatime,attr2,inode64,noquota)
tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,size=386136k,mode=700)
/root/CentOS-7-x86_64-DVD-1908.iso on /mnt type iso9660 (ro,relatime)
[root@linux ~]#



[root@linux ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0    8G  0 part /
sdb      8:16   0   10G  0 disk
└─sdb1   8:17   0   10G  0 part
sr0     11:0    1  4.4G  0 rom
[root@linux ~]#


[root@linux ~]# mount /dev/sr0 /mnt
mount: /dev/sr0 is write-protected, mounting read-only
[root@linux ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0    8G  0 part /
sdb      8:16   0   10G  0 disk
└─sdb1   8:17   0   10G  0 part
sr0     11:0    1  4.4G  0 rom  /mnt
[root@linux ~]#



# iso 파일 마운트 -o loop 옵션은 생략해도 됩니다.  
[root@linux ~]# mount CentOS-7-x86_64-DVD-1908.iso /mnt
mount: /dev/loop0 is write-protected, mounting read-only
[root@linux ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0    8G  0 part /
sdb      8:16   0   10G  0 disk
└─sdb1   8:17   0   10G  0 part
sr0     11:0    1  4.4G  0 rom
loop0    7:0    0  4.4G  0 loop /mnt
[root@linux ~]#

```

> **umount 명령어**는 mount 해제시 사용합니다.  

```no-highlight
[root@linux ~]# umount  /mnt
```
