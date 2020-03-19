+++
title = "Multipath"
weight = 16
+++

## Multipath

> Multipath 란  
> Linux 에서  Storage 와 연결하여 Volume 을 구성하여 사용하기 위한 솔류션 입니다.  
> 이중화 구성으로 하여 Lun 을 붙여 사용할수 있으며 한쪽 패스가 끊기더라도 다른 한쪽의 구성으로 사용할수 있습니다.  
> [RedHat DM Multipath Doc](https://access.redhat.com/documentation/ko-KR/Red_Hat_Enterprise_Linux/6/html-single/DM_Multipath/) 에서 확인 할수 있습니다.  



- Mutipath test  

{{% notice note %}}
Mutipath 를 테스트 하기 위해서는 vm 2대가 필요 합니다.  
iscsi-server 2Nic 구성 + iscsi-client 구성으로 하여 iscsi 로그인을 Nic 별로 진행 합니다.  
{{% /notice %}}


> iscsi-server 구성  
> targetcli 부분의 설명은 제외 합니다.  [targetcli](http://) 에서 참고 하시면 됩니다.  

- iscsi-server ip 구성  

|nic 구성   	|ip 정보   	|
|---	|---	|
|eth0   	|192.168.0.11   	|
|eth1   	|192.168.0.12   	|


* Multiapth 설치  
> multipath 설치는 iscsi-client 에서 진행 합니다.  
```no-highlight
[root@centos7 ~]# yum install -y device-mapper-multipath
```
* multipath.conf 파일 생성  
```no-highlight
/etc/multipath 디렉토리에서는 config 파일이 없습니다.
[root@centos7 ~]# ls -al /etc/multipath/
합계 12
drwxr-xr-x   2 root root    6  8월 16 23:43 .
drwxr-xr-x. 79 root root 8192 10월 16 20:04 ..
[root@centos7 ~]#

multipath.conf 를 생성 합니다.
[root@centos7 ~]# mpathconf --enable
[root@centos7 ~]# ls -al /etc/multipath.conf
-rw------- 1 root root 2415 10월 16 20:05 /etc/multipath.conf
[root@centos7 ~]#
```

* Multipath Daemon 활성화  
```no-highlight
[root@centos7 ~]# systemctl enable multipathd
[root@centos7 ~]# systemctl start multipathd
```

* iscsi login  
> iqn 을 확인 합니다.  
> 확인시 Nic 양쪽으로 확인을 합니다.  
```no-highlight
ip 를 통한 iqn 확인
[root@centos7 ~]#  iscsiadm --mode discoverydb --type sendtargets --portal 192.168.0.11 --discover
192.168.0.11:3260,1 iqn.2018-10.com.example:target2
192.168.0.11:3260,1 iqn.2018-10.com.example:target1
[root@centos7 ~]#  iscsiadm --mode discoverydb --type sendtargets --portal 192.168.0.12 --discover
192.168.0.12:3260,1 iqn.2018-10.com.example:target2
192.168.0.12:3260,1 iqn.2018-10.com.example:target1
[root@centos7 ~]#

login 한번으로 Nic 두개로 로그인을 진행 합니다.
[root@centos7 ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target2 --login
Logging in to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.11,3260] (multiple)
Logging in to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.12,3260] (multiple)
Login to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.11,3260] successful.
Login to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.12,3260] successful.
[root@centos7 ~]#


```

* iscsi 볼륨 확인  
> 볼륨 확인시 이중화 된 Volume 을 확인 할수 있습니다.  
```no-highlight
[root@centos7 ~]# cat /proc/scsi/scsi
Attached devices:
Host: scsi2 Channel: 00 Id: 00 Lun: 00
  Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
  Type:   Direct-Access                    ANSI  SCSI revision: 02
Host: scsi4 Channel: 00 Id: 00 Lun: 00
  Vendor: NECVMWar Model: VMware SATA CD01 Rev: 1.00
  Type:   CD-ROM                           ANSI  SCSI revision: 05
Host: scsi33 Channel: 00 Id: 00 Lun: 00
  Vendor: LIO-ORG  Model: lv-vol1          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 00
  Vendor: LIO-ORG  Model: lv-vol1          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi33 Channel: 00 Id: 00 Lun: 02
  Vendor: LIO-ORG  Model: lv-vol3          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 02
  Vendor: LIO-ORG  Model: lv-vol3          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi33 Channel: 00 Id: 00 Lun: 01
  Vendor: LIO-ORG  Model: lv-vol2          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 01
  Vendor: LIO-ORG  Model: lv-vol2          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
[root@centos7 ~]#
```

* multipath.conf 설정  

```no-highlight
[root@centos7 ~]# vi /etc/multipath.conf
주석이 없는 설정 부분만 참고를 위하여 작성 하였습니다.
## Use user friendly names, instead of using WWIDs as names.
defaults {
        user_friendly_names yes
        find_multipaths yes
}

# blocklist 부분은 vm 에서 사용하는 Disk 가 sda 라서 해당 부분만 blacklist 처리 하였습니다.
blacklist {
#       wwid 26353900f02796769
#       devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
#       devnode "^hd[a-z]"
        devnode "sda"
}

# 최하단의 blacklist 부분은 주석 처리 합니다.
#blacklist {
#}
```
* multipath.conf 설정참고  
> Local Disk 의 경우 blacklist 를 해야 합니다.  
> HardWare Vendor 에 따라 설정 내용이 상이 합니다.  
```no-highlight
blacklist {
#       wwid 26353900f02796769
      devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
        devnode "^hd[a-z]"
devnode "^cciss!c[0-9]d[0-9]*"
}
```


* multipath Device 확인  

> mpatha / mpathb / mpathc Device 를 확인할수 있습니다.
```no-highlight
[root@centos7 ~]# systemctl reload multipathd
[root@centos7 ~]# multipath -ll
mpathc (36001405d46b28234b334117917856bf6) dm-2 LIO-ORG ,lv-vol3
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:2 sdd 8:48 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:2 sde 8:64 active ready running
mpathb (36001405c05ff0ac733b4371a3e28011f) dm-1 LIO-ORG ,lv-vol2
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:1 sdf 8:80 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:1 sdg 8:96 active ready running
mpatha (360014050dfa89d4746f48648330154cb) dm-0 LIO-ORG ,lv-vol1
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:0 sdb 8:16 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:0 sdc 8:32 active ready running
[root@centos7 ~]#
```

* multipath Alias 설정  
> mpatha 로 구성된 Device name 을 바로 사용하여도 상관은 없지만 업무에 맞게 설정을 할수 있습니다.  
> multipath.conf 에서 alias 설정을 진행 하면 됩니다.  
```no-highlight

multipath -ll 로 확인한 정보를 이용하여 multipath.conf 파일을 수정 합니다.
mpathc (36001405d46b28234b334117917856bf6) dm-2 LIO-ORG ,lv-vol3
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:2 sdd 8:48 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:2 sde 8:64 active ready running
mpathb (36001405c05ff0ac733b4371a3e28011f) dm-1 LIO-ORG ,lv-vol2
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:1 sdf 8:80 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:1 sdg 8:96 active ready running
mpatha (360014050dfa89d4746f48648330154cb) dm-0 LIO-ORG ,lv-vol1
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:0 sdb 8:16 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:0 sdc 8:32 active ready running

multipath -ll 로 확인한 wwid 로 alias 를 설정 합니다. data1 ~ data3 까지 설정 합니다.
[root@centos7 ~]# vi /etc/multipath.conf

#blacklist {
#}

multipaths {
        multipath
                wwid    36001405d46b28234b334117917856bf6
                alias   data1
        }
        multipath {
                wwid    36001405c05ff0ac733b4371a3e28011f
                alias   data2
        }
        multipath {
                wwid    360014050dfa89d4746f48648330154cb
                alias   data3
        }
}

[root@centos7 ~]#
```
* multipath alias 확인  
```no-highlight
[root@centos7 ~]# systemctl reload multipathd
[root@centos7 ~]# multipath -ll
Oct 16 20:27:42 | missing '{' on line 99 of /etc/multipath.conf
data3 (360014050dfa89d4746f48648330154cb) dm-0 LIO-ORG ,lv-vol1
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:0 sdb 8:16 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:0 sdc 8:32 active ready running
data2 (36001405c05ff0ac733b4371a3e28011f) dm-1 LIO-ORG ,lv-vol2
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:1 sdf 8:80 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:1 sdg 8:96 active ready running
data1 (36001405d46b28234b334117917856bf6) dm-2 LIO-ORG ,lv-vol3
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:2 sdd 8:48 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:2 sde 8:64 active ready running
[root@centos7 ~]#
```

* multipath Device 파일 포멧 및 mount  
> 테스트를 위하여 data 디렉토리 생성및 data1 Device 포맷을 진행합니다.  
> multipath 로 이중화 구성이된 Lun 의 경우 /dev/mapper 및에 위치 하고 있습니다.  

```no-highlight
디렉토리를 생성 합니다.
[root@centos7 ~]# mkdir  /data

ext4로 파일 시스템을 포맷 합니다.
[root@centos7 ~]# mkfs.ext4 /dev/mapper/data1
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=1024 blocks
65536 inodes, 262144 blocks
13107 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

[root@centos7 ~]#

mount 확인
[root@centos7 ~]# mount /dev/mapper/data1 /data/
[root@centos7 ~]# df -h
Filesystem         Size  Used Avail Use% Mounted on
/dev/sda3           16G  2.0G   15G  12% /
devtmpfs           1.9G     0  1.9G   0% /dev
tmpfs              1.9G     0  1.9G   0% /dev/shm
tmpfs              1.9G   12M  1.9G   1% /run
tmpfs              1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1          2.0G  168M  1.9G   9% /boot
tmpfs              378M     0  378M   0% /run/user/0
/dev/mapper/data1  976M  2.6M  907M   1% /data
[root@centos7 ~]#
```

* mutiapth path 확인  
```no-highlight
[root@centos7 ~]# multipath -ll
Oct 16 20:34:33 | missing '{' on line 99 of /etc/multipath.conf
data3 (360014050dfa89d4746f48648330154cb) dm-0 LIO-ORG ,lv-vol1
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:0 sdb 8:16 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:0 sdc 8:32 active ready running
data2 (36001405c05ff0ac733b4371a3e28011f) dm-1 LIO-ORG ,lv-vol2
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=active
| `- 33:0:0:1 sdf 8:80 active ready running
`-+- policy='service-time 0' prio=1 status=enabled
  `- 34:0:0:1 sdg 8:96 active ready running
data1 (36001405d46b28234b334117917856bf6) dm-2 LIO-ORG ,lv-vol3
size=1.0G features='0' hwhandler='0' wp=rw
|-+- policy='service-time 0' prio=1 status=enabled
| `- 33:0:0:2 sdd 8:48 active ready running
`-+- policy='service-time 0' prio=1 status=active
  `- 34:0:0:2 sde 8:64 active ready running
[root@centos7 ~]#
```

{{% notice tip %}}
LVM Filter 설정  
스토리지 볼륨을 lvm 으로 사용시 lvm.conf 를 수정 하여 lvm 으로 사용할 Device 만 지정 할수 있습니다.  
{{% /notice %}}

{{% notice info %}}
LVM Filter 설정시 주의할점  
Local disk 로 lvm 을 사용하고 있을시 반드시 해당 내역을 추가 해야 합니다.  
{{% /notice %}}

* LVM Filter 테스트  
> local disk 를 제외한 multipath 로 구성된 볼륨을 이용하여 lvm 볼륨을 설정 합니다.  
```no-highlight
필터 라인을 찾아서 아래와 같이 추가 합니다. 
[root@centos7 ~]# vi /etc/lvm/lvm.conf
        # filter = [ "a|.*/|" ]
        filter =[ "a|/dev/mapper/*|", "r|.*|" ]

lvmdiskscan 확인
[root@centos7 ~]# lvmdiskscan
  /dev/mapper/data3 [       1.00 GiB]
  /dev/mapper/data2 [       1.00 GiB]
  /dev/mapper/data1 [       1.00 GiB]
  0 disks
  3 partitions
  0 LVM physical volume whole disks
  0 LVM physical volumes
[root@centos7 ~]#
```

* multipath 관련 명령어 정리  

|명령어    	|설명   	|
|---	|---	|
|multipath -d   	|멀티패스 디바이스의 정보를 표시 합니다.   	|
|multipath -v2   	|멀티패스 디바이스를 설정하고 설정된 정보를 출력합니다.   	|
|multipath -v3   	|멀티패스 디바이스를 설정하고 설정된 정보를 출력합니다.   	|
|multipath -ll   	|설정된 멀티패스 디바이스의 정보를 출력합니다.     	|
|multipath -F   	|모든 미사용 멀티패스 디바이스 정보를 Flush 시킵니다.실제 디바이스는 제거 하지 않습니다.     	|
|echo 1 > /sys/class/fc_host/host번호/issue_lib   	|온라인 상태에서 멀티패스 인식 / fc포트 갯수 만큼 host 번호를 찾아 1값을 넣으면 됩니다.    	|
|multipath -r    	|멀티패스를 인식시 사용     	|
|systemctl reload multipathd   	|멀티패스를 인식시 사용    	|
|lspci| grep -i fibre   	|HBA 정보 확인    	|
|scsi_id --whitelisted --device=/dev/sda   	|특정 device wwid 확인    	|
|/lib/udev/scsi_id --replace-whitespace --whitelisted --device /dev/sdb   	|특정 device wwid 확인    	|
|scsi_id –g –u –s /dev/sda  	|Centos5 에서 wwid 확인    	|
|systool -av -c fc_host | grep speed   	|HBA Speed 확인    	|
|systool -c fc_host -v   	|HBA 카드 정보 확인 path link port wwn 확인    	|
|systool -c fc_remote_ports -v –d   	|HBA카드에 연결된 타겟 디바이스의 wwpn , wwnn 확인    	|
|systool -c scsi_disk -v   	|스토리지의 캐시타입 , RAID 타입 , Time OUT , 제조사 확인    	|

```no-highlight
HBA 정보 확인  
# lspci| grep -i fibre
```

{{% notice note %}}
multipath 관련 패키지  
systool 패키지 sysfsutils  
scsi-recan 패키지 sg3_utils  
{{% /notice %}}


<br></br>