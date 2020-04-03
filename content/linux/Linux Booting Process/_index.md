+++
title = "Linux Booting Process"
weight = 2
+++


## Linux Booting Process

* Linux 부팅 과정

{{% notice note %}}  
Linux 부팅 과정은 장애 처리에 있어 핵심적인 요소 입니다.  
부팅까지 완전히 끝났다고 하면 다른 부팅 과정을 빼고 다른 부분의 장애 포인트를 트러블 슈팅 하여 문제를 해결 할수 있습니다.  
CentOS7 에서는 기존에 사용되던 SysV init 스크립트를 systemd 로 대체 하였습니다.  
{{% /notice %}}  

* Linux Booting 시 수행되는 과정  

|단계    	|단계별 설명   	|
|---	|---	|
|1 단계    	|컴퓨터의 Bios 가 post 를 수행 합니다.    	|
|2 단계    	|Bios 가 부트 로더의 MBR 을 읽습니다.    	|
|3 단계    	|GRUB2 부트로더 에서 vmlinuz 커널 이미지를 로드 합니다.    	|
|4 단계    	|GRUB2 부트로더 에서 initramfs 이미지의 내용을 추출합니다.    	|
|5 단계    	|initramfs에서 드라이버 모듈을 로드 합니다.     	|
|6 단계    	|시스템의 첫번째 프로세스인 systemd를 시작 합니다.    	|
|7 단계    	|systemd 프로세스가 작업을 수행 합니다.    	|

|7단계 systemd 프로세스 작업 Detail   	|
|---	|
| /etc/systemd 디렉토리에서 구성 파일을 읽습니다.   	|
| /etc/systemd/system/default.target에 의해 링크 된 파일을 읽습니다.  	|
| system target 으로 정의된 시스템 상태로 시스템으로 가져옵니다.   	|
| /etc/rc.local 을 실행 합니다.   	|


![D2](/linux/linux_tmp/info/D2.png)  

**1) POST ( Power on Self Test)**  

> Universal Extended Firmware Interface (UEFI) 또는 Basic Input Output System (BIOS) 
> 시스템 펌웨어에서 POST (Power-On Self-Test)가 실행되고 하드웨어를 시작하는 데 필요한 하드웨어 시스템이 초기화됩니다.

**2) MBR Bootable**  


![D3](/linux/linux_tmp/info/D3.png)  

* MBR (마스터 부트 레코드)은 BIOS에서 메모리로 읽는 부팅 드라이브의 처음 512 바이트입니다.  
* 다음 64 바이트는 디스크의 파티션 테이블을 포함합니다. 마지막 두 바이트는 오류 탐지에 사용되는 "매직 번호"입니다.  
* MBR은 부팅 가능한 장치를 발견하고 GRUB2 부트 로더를 메모리에로드하고 그에 대한 제어를 전달합니다.  


## GRUB2

**3) GRUB2 Boot Loader**  

- RHEL 7에서 사용되는 기본 부트 로더 프로그램은 GRUB 2입니다. GRUB는 GRand Unified Bootloader의 약자입니다. GRUB 2는 레거시 GRUB라고도 불리는 이전 GRUB 부트 로더를 대체합니다.
- GRUB 2 구성 파일은 /boot/grub2/grub.cfg에 있습니다 (이 파일을 직접 편집하지 마십시오).  

```no-highlight
### BEGIN /etc/grub.d/10_linux ###
menuentry 'CentOS Linux (3.10.0-862.11.6.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-862.11.6.el7.x86_64-advanced-96e4bd20-d719-4d7b-a495-12c4867847c4' {
        load_video
        set gfxpayload=keep
        insmod gzio
        insmod part_msdos
        insmod xfs
        set root='hd0,msdos1'
        if [ x$feature_platform_search_hint = xy ]; then
          search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  4cae8e08-00be-4af6-acb8-49388e4c102d
        else
          search --no-floppy --fs-uuid --set=root 4cae8e08-00be-4af6-acb8-49388e4c102d
        fi
        linux16 /vmlinuz-3.10.0-862.11.6.el7.x86_64 root=UUID=96e4bd20-d719-4d7b-a495-12c4867847c4 ro crashkernel=auto rhgb quiet
        initrd16 /initramfs-3.10.0-862.11.6.el7.x86_64.img
}
```

*  **GRUB2 메뉴 구성 설정은 grub.cfg를 생성 할 때 /etc/default/grub 에서 가져옵니다.**  
```no-highlight
[root@centos7 ~]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
[root@centos7 ~]#
```

* **/etc/default/grub 파일 수정시 grub2-mkconfig 명령이 실행 되어야 합니다.**  
```no-highlight
[root@centos7 ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
```
> - GRUB2는 / boot 디렉토리의 vmlinuz라고도하는 압축 된 커널 이미지 파일을 검색합니다.
> - GRUB2는 vmlinuz 커널 이미지 파일을 메모리에로드하고 initramfs 이미지 파일의 내용을 임시 메모리 기반 파일 시스템 (tmpfs)으로 추출합니다.
> - 초기 RAM 디스크 (initrd)는 실제 루트 파일 시스템보다 먼저 마운트되는 초기 루트 파일 시스템입니다.


* **initramfs**

> - 초기 RAM 파일 시스템의 작업은 IDE, SCSI 또는 RAID와 같은 블록 장치 모듈을 사전로드하여 해당 모듈이 정상적으로 상주하는 루트 파일 시스템에 액세스하고 마운트 할수 있도록합니다. 
> - initramfs는 커널에 바인딩되며 커널은이 initramfs를 2 단계 부팅 프로세스의 일부로 마운트합니다.
> - dracut 유틸리티는 새로운 커널이 설치 될 때마다 initramfs를 생성합니다.
> - lsinitrd 명령을 사용하여 dracut에 의해 작성된 이미지의 내용을보십시오.

```no-highlight
[root@centos7 ~]# lsinitrd |less
```
**4) Kernel 로드**  

> 커널은 프로세스 ID가 1 인 systemd 프로세스를 시작합니다.  
```no-highlight
[root@centos7 ~]# ps -ef |grep systemd
root          1      0  0 14:20 ?        00:00:03 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
```

> - initrd 이미지에서 필요한 드라이버 모듈을로드합니다.  
> - initramfs는 부팅에 필요한 모든 하드웨어의 커널 모듈과 부팅의 다음 단계로 진행하는 데 필요한 초기 스크립트를 포함합니다.  

## systemd

**5) Systemd 시작**  

> PID 가 1인 systemd 프로세스가 커널에 의해 시작 합니다.  
```no-highlight
[root@centos7 ~]# ps -ef |grep systemd
root          1      0  0 14:20 ?        00:00:03 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
```
> - systemd는 시스템 부팅 후 시작되는 첫번째 프로세스이며 시스템이 종료 될때 까지 실행되는 최종 프로세스입니다.  
> - 부팅의 최종 단계를 제어하고 시스템을 사용할 준비를합니다. 또한 서비스를 동시에로드하여 부팅 속도를 높입니다.  
> - systemd 는 /etc/systemd/system/default.target 에 링크된 파일을 읽습니다.  

* Run Level

|Run Level   	|Target Units   	|설명    	|
|---	|---	|---	|
|0   	|runlevel0.target, poweroff.target   	|종료 및 전원off   	|
|1   	|runlevel1.target, rescue.target   	|Rescue shell   	|
|2,3,4   	|runlevel[234].target, multi- user.target	   	|그래픽을 사용하지 않는 multi-user shell   	|
|5   	|runlevel5.target, graphical.target   	|그래픽을 사용하는 multi-user shell   	|
|6   	|runlevel6.target, reboot.target   	|system rebooting   	|



|Systemd 에 의해 실행되는 시스템 초기화 작업      	|
|---	|
| 호스트 이름 설정   	|
| 네트워크 초기화   	|
| 설정에 따라 SELinux 초기화하기   	|
| 환영 배너 출력   	|
| 커널 부팅 인수를 기반으로 시스템 하드웨어 초기화   	|
| /proc 파일 시스템과 같은 가상 파일 시스템을 포함하여 파일 시스템 마운트   	|
| /var에있는 디렉토리 정리하기   	|
| 스와핑 시작하기   	|

* System target 확인  
```no-highlight
[root@centos7 ~]# systemctl get-default
multi-user.target
```

* Run Level 로 확인  
```no-highlight
[root@centos7 ~]# runlevel
N 3
```

* default.target Link 확인  
```no-highlight
[root@centos7 ~]# ls -al /etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 37 Sep 29 06:02 /etc/systemd/system/default.target -> /lib/systemd/system/multi-user.target
```


* booting시 장애 증상별 체크 포인트  

{{% notice info %}}
부팅장애시 확인 할수 있는 체크 포인트 입니다.  
{{% /notice %}}


|부팅 단계    	|설명    	|
|---	|---	|
|POST   	|H/W구성    	|
|부팅 장치선택    	|BIOS/UEFI 설정 또는 H/W 부팅메뉴   	|
|boot loader 로드    	|grub2-install and /etc/defaults/grub 파일    	|
|kernel 로드    	|GRUB 설정 및 /etc/dracut.conf    	|
|/sbin/init 실행    	|initramfs 재생성    	|
|initrd.target   	|initramfs 재생성    	|
|Filesystem mount , Mainternance mode   	|/etc/fstab 확인    	|
|default target 실행    	|/etc/systemd/system/default.target   	|


<br></br>
