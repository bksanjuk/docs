+++
title = "Install"
weight = 3
+++

## Install

- CentOS 7 설치  

{{% notice note %}}
설치환경: vm  
Cpu 1core / Memory 1G / Hdd 20G  
DVD Image 로 부팅 하면 최초 설치 화면을 볼수 있습니다.  
OS 장애시에도 DVD Image 로 부팅을 하여 rescue mode 로 진입 합니다.  
{{% /notice %}}

* Install CentOS 7 을 선택합니다.
![설치화면1](/centos/centos_tmp/install/k1.png)
<br> </br>

- 설치언어선택  

{{% notice note %}}
사용할 언어를 선택 합니다. Desktop 환경에서는 한글을 사용하겠지만  
서버 환경에는 Default 로 선택 되어 있는 English 로 사용하는 것이 좋습니다.  
{{% /notice %}}

![설치화면2](/centos/centos_tmp/install/k2.png)
<br></br>

* INSTALLATION SUMMARY
![설치화면3](/centos/centos_tmp/install/k3.png)
<br></br>

- LOCALIZATION  

{{% notice note %}}
DATE&TIME: System 에서 사용할 Timezone 을 설정할수 있습니다.  
KEYBOARD: System 에서 사용할 키보드 선택  
LANGUAGE SUPPORT: System 에서 사용할 언어설정
{{% /notice %}}

- SOFTWARE  

{{% notice note %}}
INSTALLATION SOURCE: 설치 미디어 선택 NETWORK 설치 및 CD-ROM 으로 설치 할수 있습니다.  
SOFTWARE SELECTION: OS 설치시 설치할 패키지 선택  
{{% /notice %}}


- SYSTEM  

{{% notice note %}}
INSTALLATION DESTINATION: OS 설치가 될 HardDisk 의 파티션작업을 할수 있습니다.  
KDUMP: OS 장애시 KDUMP 설정을 통하여 OS DUMP 를 생성할수 있습니다.  
NETWORK & HOSTNAME: IP 설정 및 System Hostname 을 설정할수 있습니다.  
SECURITY POLICY: System 보안 설정을 할수 있습니다.  
{{% /notice %}}

<br></br>

* 설정 DATE & TIME 을 클릭하여 Timezone 을 변경 합니다.  
Asia Seoul 로 변경후 Done 을 클릭합니다.  
![설치화면4](/centos/centos_tmp/install/k4.png)
<br></br>

* SYSTEM 항목에서 INSTALLATION DESTINATION 을 선택합니다.  
![설치화면5](/centos/centos_tmp/install/k5.png)

- Installation destination  

{{% notice note %}}
Other Storage Options 에 Partitioning ,
Automatically configure partitioning 이 Default 로 선택 되어 있습니다.
I will configure partitioning 으로 변경후 Done 을 클릭합니다.
{{% /notice %}}

<br></br>

* MANUAL PARTITIONING (수동파티션 설정)
![설치화면6](/centos/centos_tmp/install/k6.png)

- 파티셔닝 참고  

{{% notice note %}}
/boot 2G    (kernel-update 시 boot 용량이 증가 합니다.)  
swap        (Memory 용량의 약 1배 ~ 2배로 설정 합니다.)  
16G이상부터는 반배나 1배로 설정 합니다.  
/           (별도의 파티션을 사용하지 않는다면 최대 용량으로 설정 합니다.)  
Test VM 의 경우 /boot , swap / 구성으로 설치 합니다.  
/home       (일반 사용자 디렉토리, 사용용량에 맞게 설정합니다.)  
/var        (log 및 db data 의 Default 저장 디렉토리 입니다.  
log 저장을 많이 하는 System 의 경우 Size 를 크게 설정합니다.)  
KDUMP 이용시 /var/crash 가 설정 되어야 하며 일반적으로 메모리 용량의 약 1.5배입니다.  
에서 Standard Partition 을 선택 합니다.  
+ 버튼을 눌러 파티션을 추가 할수 있습니다.  
{{% /notice %}}

<br></br>


* /boot 파티션 생성
![설치화면7](/centos/centos_tmp/install/k7.png)

- boot 디렉토리  

{{% notice note %}}
/boot 디렉토리 2G Size 로 파티션을 생성합니다.  
Add mount point 를 클릭합니다.  
Mount point: 경로설정  
Desired Capacity: 용량  
Add mount point 를 클릭하여 boot 파티션을 생성합니다.  
{{% /notice %}}
<br></br>

* swap 파티션 생성
![설치화면8](/centos/centos_tmp/install/k8.png)

- swap 참고  

{{% notice note %}}
swap 의 경우 별도로 Mount Point 가 없습니다.
Mount Point: swap
Desired Capacity: 4G 로 설정합니다.
Add mount point 를 클릭하여 swap 파티션을 생성합니다.
{{% /notice %}}

<br></br>

* / 파티션 생성
![설치화면9](/centos/centos_tmp/install/k9.png)

- / 디렉토리  

{{% notice note %}}
Mount Point: /
Desired Capacity: 용량이 설정 되지 않으면 HardDisk 의 최대 용량으로 설정 됩니다.
Add mount point 를 클릭하여 / 파티션을 생성 합니다.
{{% /notice %}}

- **UEFI 사용시 참고**

{{% notice info %}}
UEFI 사용시 /boot/efi 디렉토리를 생성 해야 합니다.
400m 정도로 설정을 합니다.
{{% /notice %}}

![설치화면9-1](/centos/centos_tmp/install/k9-1.png)


<br></br>

* 파티셔닝 작업이 끝났습니다.
Done 을 클릭합니다.
![설치화면10](/centos/centos_tmp/install/k10.png)
<br></br>

* SUMMARY OF CHANGES 변경사항 요약
![설치화면11](/centos/centos_tmp/install/k11.png)

* Accept Changes 를 클릭합니다.
<br></br>

- INSTALLATION SUMMARY  

{{% notice note %}}
처음 설정 하였던 화면으로 돌아 왔습니다.
추가적인 패키지 설치 및 NETWORK 설정 변경등의 작업은 차후에 진행할수 있습니다.
Begin Install 을 클릭 하여 설치를 진행합니다.
{{% /notice %}}

![설치화면12](/centos/centos_tmp/install/k12.png)
<br></br>

- CONFIGURATION  

{{% notice note %}}
Begin Installation 을 클릭하면 파티셔닝 작업후 선택한 패키지로 설치가 진행됩니다.
USER SETTINGS 에서는 ROOT PASSWORD 설정, USER CREATION 을 진행할수 있습니다.
ROOT PASSWORD 를 선택하여 관리자 암호를 설정합니다.
{{% /notice %}}


![설치화면13](/centos/centos_tmp/install/k13.png)
<br></br>

* ROOOT PASSWORD 관리자 암호설정
![설치화면14](/centos/centos_tmp/install/k14.png)
<br></br>

* OS 설치가 완료 되었습니다.
Reboot 를 클릭하여 설치를 마무리 합니다.
![설치화면14](/centos/centos_tmp/install/k14.png)

<br></br>

- anaconda-ks file  

{{% notice tip %}}
설치후 anaconda 파일을 통하여 PXE Server 구성후 동일한 패키지로 설치를 진행 할수 있습니다.
anaconda-ks file 은 /root 디렉토리에 위치 하고 있습니다.
PXE 설치의 경우 차후 문서를 만들 예정 입니다.
{{% /notice %}}

* anaconda-ks.cfg
```no-highlight
[root@centos7 ~]# pwd
/root
[root@centos7 ~]# cat anaconda-ks.cfg
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
network  --bootproto=dhcp --device=ens34 --onboot=off --ipv6=auto
network  --hostname=localhost.localdomain

# Root password
rootpw --iscrypted $1$AUaB1Lb335VMaYzw$dF0lijf4FCGOgfYPRZxF1Yvc7pQgDGtAi2eVbMUq8LaxYYILJxN4/OHzZdAZaSx/5BZwXGT5UohsK6wYtfGYr/
# System services
services --disabled="chronyd"
# System timezone
timezone Asia/Seoul --isUtc --nontp
user --name=test --password=$6$zDTuta3/36Bo2j./$sb7/EbJQECn3Xe3HiRanTzYv6lZrP0zaMdij2Cx6I/5gz5afmrSCp.WNqEKEa9chD8gX7O1NT7RYvxbbI4O3.. --iscrypted --gecos="test"
# System bootloader configuration
bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=sda
# Partition clearing information
clearpart --none --initlabel
# Disk partitioning information
part swap --fstype="swap" --ondisk=sda --size=2048
part /boot --fstype="xfs" --ondisk=sda --size=2048
part / --fstype="xfs" --ondisk=sda --size=16383

%packages
@^minimal
@compat-libraries
@core
@development
kexec-tools

%end

%addon com_redhat_kdump --enable --reserve-mb='auto'

%end

%anaconda
pwpolicy root --minlen=6 --minquality=1 --notstrict --nochanges --notempty
pwpolicy user --minlen=6 --minquality=1 --notstrict --nochanges --emptyok
pwpolicy luks --minlen=6 --minquality=1 --notstrict --nochanges --notempty
%end
[root@centos7 ~]#
```
