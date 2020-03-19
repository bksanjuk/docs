+++
title = "YUM"
weight = 8
+++

## Local yum repository

- Local yum repository  

{{% notice note %}}
RHEL 등 CentOS 기술지원시 외부 yum repository 를 사용할수 없을 경우에 localeyum 을 구성하여 사용합니다.  
문서에서는 dvd-rom 을 이용한 방법을 간단히 설명합니다.
{{% /notice %}}

<br></br>

* dvd-rom mount 확인
/mnt 디렉토리에 dvd-rom 을 마운트 합니다
```no-highlight
[root@centos7 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0    2G  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0   16G  0 part /
sr0     11:0    1  4.2G  0 rom
[root@centos7 ~]#
```

* /etc/yum.repos.d/iso.repo 를 생성합니다.
```no-highlight
[root@centos7 ~]# mount /dev/sr0 /mnt
mount: /dev/sr0 is write-protected, mounting read-only

CentOS 의 경우 yum.repos.d 디렉토리의 repo 파일을 백업 합니다.
RHEL 에서는 별도의 백업없이 iso.repo 파일을 만드시면 됩니다.
[root@centos7 ~]# cd /etc/yum.repos.d/
[root@centos7 yum.repos.d]# mv *.repo back/

[root@centos7 ~]# vi /etc/yum.repos.d/iso.repo
[iso]
name=iso
baseurl=file:///mnt/
gpgcheck=0
```


* local yum 확인
```no-highlight
[root@centos7 ~]# yum clean all
[root@centos7 ~]# yum list
```
<br></br>

## YUM
> CentOS / RHEL 에서는 패키지 관리에 yum 을 사용합니다.  
> dnf 명령어의 경우 centos7 에서는 기본으로 들어 있지 않습니다.  


```no-highlight
[root@centos7 ~]# yum --help
Loaded plugins: fastestmirror
Usage: yum [options] COMMAND

List of Commands:

check          Check for problems in the rpmdb
check-update   Check for available package updates
clean          Remove cached data
deplist        List a package's dependencies
distribution-synchronization Synchronize installed packages to the latest available versions
downgrade      downgrade a package
erase          Remove a package or packages from your system
fs             Acts on the filesystem data of the host, mainly for removing docs/lanuages for minimal hosts.
fssnapshot     Creates filesystem snapshots, or lists/deletes current snapshots.
groups         Display, or use, the groups information
help           Display a helpful usage message
history        Display, or use, the transaction history
info           Display details about a package or group of packages
install        Install a package or packages on your system
list           List a package or groups of packages
load-transaction load a saved transaction from filename
makecache      Generate the metadata cache
provides       Find what package provides the given value
reinstall      reinstall a package
repo-pkgs      Treat a repo. as a group of packages, so we can install/remove all of them
repolist       Display the configured software repositories
search         Search package details for the given string
shell          Run an interactive yum shell
swap           Simple way to swap packages, instead of using shell
update         Update a package or packages on your system
update-minimal Works like upgrade, but goes to the 'newest' package match which fixes a problem that affects your system
updateinfo     Acts on repository update information
upgrade        Update packages taking obsoletes into account
version        Display a version for the machine and/or available repos.
~ 중략
```
<br></br>

* yum repository list 확인
```no-highlight
[root@centos7 ~]# yum repolist
```

* 패키지 검색
```no-highlight
[root@centos7 ~]# yum search wget
```

* 패키지 설치
```no-highlight
[root@centos7 ~]# yum install wget
```

* 패키지 삭제
```no-highlight
[root@centos7 ~]# yum remove wget
```
* 패키지 다운로드
```no-highlight

패키지를 설치 하고 다운로드 합니다.
[root@centos7 ~]# yum install wget --downloadonly

다운로드 위치
/var/cache/yum/x86_64/7/base/packages

다운로드 위치 지정
[root@centos7 ~]# yum install wget --downloadonly --downloaddir=/root

pkg 설치 없이 다운로드만 합니다.
[root@centos7 ~]# yum install wget -y --downloadonly
다운로드 위치
/var/cache/yum/x86_64/7/base/packages
```

* yum history
> yum 으로 설치 하고 지운내역을 확인 할수 있습니다.  
> undo 를 통하여 패키지 설치 전으로 되돌릴수 있습니다.  

```no-highlight
[root@centos7 ~]# yum history list
Loaded plugins: fastestmirror
ID     | Login user               | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     4 | root <root>              | 2018-09-29 08:14 | Erase          |    1
     3 | root <root>              | 2018-09-29 08:11 | Install        |    1
     2 | root <root>              | 2018-09-29 07:46 | I, O, U        |  215 EE
     1 | System <unset>           | 2018-09-29 05:58 | Install        |  419
history list
[root@centos7 ~]#

명령어 사용시 yum history undo $ID값 을 입력하면 됩니다.
[root@centos7 ~]# yum history undo 2
```

* yum localinstall

> rpm 파일을 yum 명령어를 통하여 설치 할수 있습니다.  
> 의존성 문제가 있는 rpm 파일설치에 사용합니다.  
```no-highlight
[root@centos7 ~]# yum localinstall wget-1.14-15.el7_4.1.x86_64.rpm
```



* 문자열이 포함된 패키지 확인
```no-highlight
yum provides */문자열
[root@centos7 ~]# yum provides */httpd.conf
```

* System 패키지 upgrade

> yum update 와 yum upgrade 차이점  
> yum update 의 경우 설치된 모든 패키지를 업데이트 합니다.  
> 사용하지 않는 패키지 보존  
> yum upgrade 의 경우 더이상 사용하지 않는 패키지도 강제적으로 업데이트 합니다.  
> 더이상 사용하지 않는 패키지 삭제  
<p>


{{% notice tip %}}
yum update 가 더 안전하다고 알려져 있습니다.  
{{% /notice %}}

```no-highlight
[root@centos7 ~]# yum update
```
<br></br>



