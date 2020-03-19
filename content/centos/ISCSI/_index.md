+++
title = "ISCSI"
weight = 15
+++

## ISCSI

{{% notice note %}}
CentOS 7 ISCSI Server 사용시 targetCLI 를 사용합니다.  
[targetCLI](http://linux-iscsi.org/wiki/Targetcli)에서 자세한 정보를 확인 할수 있습니다.  
{{% /notice %}}


{{% notice tip %}}
 문서내용 참고  
targetcli(iscsi server) 에서 fileio 생성 -> iscsi client 설치  -> fileio 테스트  
block 생성 -> block 테스트 로 문서를 정리 하였습니다.  
iscsi client 는 별도의 vm 에 구성을 해야 합니다.  
{{% /notice %}}

* targetCLI(iscsi server) 설치  
```no-highlight
[root@centos7 ~]# yum install -y targetcli
```

* targetCLI Daemon 활성화 및 Daemon 실행  
```no-highlight
[root@centos7 ~]# systemctl enable target
[root@centos7 ~]# systemctl start target
```

*  사용할수 있는 Volume  

|Volume 종류   	|설명     	|
|---	|---	|
|block    	|lvm 논리볼륨 , Disk    	|
|fileio    	|지정된 크기의 File 생성후 사용       	|
|pscsi    	|물리적 scsi 장치     	|
|ramdisk    	|ramdisk 생성후 사용    	|


* fileio 사용 example  

{{% notice tip %}}
diksk1.img 파일생성 Tip  
/> 위치에서 생성을 해야 합니다. 위치 이동은 cd 명령어를 통하여 이동할수 있습니다.  
{{% /notice %}}


> targetcli 에서 사용할 디렉토리 생성후 10G disk1.img 파일을 생성 합니다.  
```no-highlight
[root@centos7 ~]# mkdir /images
[root@centos7 ~]# targetcli
targetcli shell version 2.1.fb46
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/> backstores/fileio create disk1 /images/disk1.img 10G
Created fileio disk1 with size 10737418240
```

* wwn 생성 및 Lun 생성  
```no-highlight

/> cd iscsi/iqn.2018-10.com.example:target1/tpg1/
/iscsi/iqn.20...:target1/tpg1> ls
o- tpg1 ..................................................................................................... [no-gen-acls, no-auth]
  o- acls ................................................................................................................ [ACLs: 0]
  o- luns ................................................................................................................ [LUNs: 0]
  o- portals .......................................................................................................... [Portals: 1]
    o- 0.0.0.0:3260 ........................................................................................................... [OK]
/iscsi/iqn.20...:target1/tpg1> portals/ create
Using default IP port 3260
Binding to INADDR_ANY (0.0.0.0)
This NetworkPortal already exists in configFS
/iscsi/iqn.20...:target1/tpg1> luns/ create /backstores/fileio/disk1
Created LUN 0.
```

* ACL Disable 및 Read/Write mode 설정  
```no-highlight
/iscsi/iqn.20...:target1/tpg1> set attribute demo_mode_write_protect=0
Parameter demo_mode_write_protect is now '0'.
/iscsi/iqn.20...:target1/tpg1> set attribute generate_node_acls=1
Parameter generate_node_acls is now '1'.
/iscsi/iqn.20...:target1/tpg1> exit
Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup/.
Configuration saved to /etc/target/saveconfig.json
[root@centos7 ~]#
```

* iscsi client 설치  

> 별도 vm 에서 iscsi-initiator-utils 를 설치 합니다.  

```no-highlight
[root@CentOS7-client ~]# yum install -y iscsi-initiator-utils
```

* fileio 테스트  
> iscsiadmo 을 이용하여 targetcli iqn 정보를 확인 합니다.  
```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode discoverydb --type sendtargets --portal 192.168.0.40 --discover
192.168.0.40:3260,1 iqn.2018-10.com.example:target1
[root@CentOS7-client ~]#
```

* iscsi login  

```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target1 --login
Logging in to [iface: default, target: iqn.2018-10.com.example:target1, portal: 192.168.0.40,3260] (multiple)
Login to [iface: default, target: iqn.2018-10.com.example:target1, portal: 192.168.0.40,3260] successful.
[root@CentOS7-client ~]#
```


* iscsi 확인  

{{% notice tip %}}
iscsi client 확인  
iscsi 확인시 proc 파일시스템의 scsi 파일 과 fdisk 정보 에서 확인 할수 있습니다.  
{{% /notice %}}

```no-highlight
Model: disk1 를 확인 할수 있습니다.

[root@CentOS7-client ~]# cat /proc/scsi/scsi
Attached devices:
Host: scsi2 Channel: 00 Id: 00 Lun: 00
  Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
  Type:   Direct-Access                    ANSI  SCSI revision: 02
Host: scsi4 Channel: 00 Id: 00 Lun: 00
  Vendor: NECVMWar Model: VMware SATA CD01 Rev: 1.00
  Type:   CD-ROM                           ANSI  SCSI revision: 05
Host: scsi33 Channel: 00 Id: 00 Lun: 00
  Vendor: LIO-ORG  Model: disk1            Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05


/dev/sdb 10G 를 확인 할수 있습니다.

[root@CentOS7-client ~]# fdisk  -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009fcf9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

Disk /dev/mapper/centos-root: 18.2 GB, 18249416704 bytes, 35643392 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 4194304 bytes

[root@CentOS7-client ~]#

```

* iscsi logout
-u 옵션을 사용하여 로그아웃 후 -o 옵션을 사용하여 delete 합니다.  
```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target1 --portal 192.168.0.40 -u
Logging out of session [sid: 1, target: iqn.2018-10.com.example:target1, portal: 192.168.0.40,3260]
Logout of [sid: 1, target: iqn.2018-10.com.example:target1, portal: 192.168.0.40,3260] successful.
[root@CentOS7-client ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target1 --portal 192.168.0.40 -o delete
```

* block 생성  
> targetcli server 에서 block 생성용으로 /dev/sdb 10G 를 추가 하였습니다.  
> /dev/sdb 10G 를 이용하여 lvm 3개를 만들어 테스트를 진행 합니다.  

{{% notice note %}}
file system type lvm  
fdisk 에서 별도로 file system type 을 lvm 으로 지정 하지 않아도 사용시 문제가 없습니다.  
볼륨 재사용시 파일 시스템 확인용으로 사용합니다.  
{{% /notice %}}

```no-highlight
[root@centos7 ~]# fdisk  -l
Fdisk 확인 
Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a27c3

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     4196351     2097152   83  Linux
/dev/sda2         4196352     8390655     2097152   82  Linux swap / Solaris
/dev/sda3         8390656    41943039    16776192   83  Linux

Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

[root@centos7 ~]#

fdisk 작업 
(별도의 fdisk 작업을 하지 않아도 lvm 사용시 문제는 없지만 차후 file system 확인용도로 
partition type 을 지정 합니다.)
[root@centos7 ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x852441a8.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519):
Using default value 20971519
Partition 1 of type Linux and of size 10 GiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@centos7 ~]#
```


* LVM 생성  
>targetcli block 으로 사용할 lvm 을 생성 합니다.  
```no-highlight
[root@centos7 ~]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
[root@centos7 ~]# vgcreate vg00 /dev/sdb1
  Volume group "vg00" successfully created
[root@centos7 ~]# lvcreate -L 1G -n lv-vol1 vg00
  Logical volume "lv-vol1" created.
[root@centos7 ~]# lvcreate -L 1G -n lv-vol2 vg00
  Logical volume "lv-vol2" created.
[root@centos7 ~]# lvcreate -L 1G -n lv-vol3 vg00
  Logical volume "lv-vol3" created.
[root@centos7 ~]# lvs
  LV      VG   Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv-vol1 vg00 -wi-a----- 1.00g                                                 
  lv-vol2 vg00 -wi-a----- 1.00g                                                 
  lv-vol3 vg00 -wi-a----- 1.00g                                                 
[root@centos7 ~]#
```

* iscsi block Device 생성  
```no-highlight
[root@centos7 ~]# targetcli
targetcli shell version 2.1.fb46
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/iscsi/iqn.20...:target1/tpg1> cd

/> cd /backstores/block
/backstores/block> create lv-vol1 /dev/vg00/lv-vol1
Created block storage object lv-vol1 using /dev/vg00/lv-vol1.
/backstores/block> create lv-vol2 /dev/vg00/lv-vol2
Created block storage object lv-vol2 using /dev/vg00/lv-vol2.
/backstores/block> create lv-vol3 /dev/vg00/lv-vol3
Created block storage object lv-vol3 using /dev/vg00/lv-vol3.
/backstores/block>
```


* wwn 생성  
```no-highlight
/backstores/block> cd /iscsi
/iscsi> create iqn.2018-10.com.example:target2
Created target iqn.2018-10.com.example:target2.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.
/iscsi>
```

* Lun 생성  
```no-highlight
/iscsi> cd iqn.2018-10.com.example:target2/
/iscsi/iqn.20...ample:target2> ls
o- iqn.2018-10.com.example:target2 ....................................................................................... [TPGs: 1]
  o- tpg1 ................................................................................................... [no-gen-acls, no-auth]
    o- acls .............................................................................................................. [ACLs: 0]
    o- luns .............................................................................................................. [LUNs: 0]
    o- portals ........................................................................................................ [Portals: 1]
      o- 0.0.0.0:3260 ......................................................................................................... [OK]
/iscsi/iqn.20...ample:target2> cd tpg1/luns
/iscsi/iqn.20...et2/tpg1/luns> create /backstores/block/lv-vol1
Created LUN 0.
/iscsi/iqn.20...et2/tpg1/luns> create /backstores/block/lv-vol2
Created LUN 1.
/iscsi/iqn.20...et2/tpg1/luns> create /backstores/block/lv-vol3
Created LUN 2.
/iscsi/iqn.20...et2/tpg1/luns> ls
o- luns .................................................................................................................. [LUNs: 3]
  o- lun0 ................................................................... [block/lv-vol1 (/dev/vg00/lv-vol1) (default_tg_pt_gp)]
  o- lun1 ................................................................... [block/lv-vol2 (/dev/vg00/lv-vol2) (default_tg_pt_gp)]
  o- lun2 ................................................................... [block/lv-vol3 (/dev/vg00/lv-vol3) (default_tg_pt_gp)]
/iscsi/iqn.20...et2/tpg1/luns>
```

* ACL Disable 및 Read/Write mode 설정  
```no-highlight
/iscsi/iqn.20...et2/tpg1/luns> cd

/iscsi/iqn.20...:target2/tpg1> set attribute authentication=0
Parameter authentication is now '0'.
/iscsi/iqn.20...:target2/tpg1> set attribute demo_mode_write_protect=0
Parameter demo_mode_write_protect is now '0'.
/iscsi/iqn.20...:target2/tpg1> set attribute generate_node_acls=1
Parameter generate_node_acls is now '1'.
/iscsi/iqn.20...:target2/tpg1> exit
Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup/.
Configuration saved to /etc/target/saveconfig.json
[root@centos7 ~]#
```


* block 테스트  
> iscsi client 에서 iscsiadm 을 이용하여 targetcli iqn 을 확인 합니다.  
```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode discoverydb --type sendtargets --portal 192.168.0.40 --discover
192.168.0.40:3260,1 iqn.2018-10.com.example:target1
192.168.0.40:3260,1 iqn.2018-10.com.example:target2
[root@CentOS7-client ~]#
```

* iscsi login  
```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target2 --portal 192.168.0.40 --login
Logging in to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.40,3260] (multiple)
Login to [iface: default, target: iqn.2018-10.com.example:target2, portal: 192.168.0.40,3260] successful.
[root@CentOS7-client ~]#
```

* iscsi 확인  
```no-highlight
[root@CentOS7-client ~]# cat /proc/scsi/scsi
Attached devices:
Host: scsi2 Channel: 00 Id: 00 Lun: 00
  Vendor: VMware,  Model: VMware Virtual S Rev: 1.0
  Type:   Direct-Access                    ANSI  SCSI revision: 02
Host: scsi4 Channel: 00 Id: 00 Lun: 00
  Vendor: NECVMWar Model: VMware SATA CD01 Rev: 1.00
  Type:   CD-ROM                           ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 00
  Vendor: LIO-ORG  Model: lv-vol1          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 01
  Vendor: LIO-ORG  Model: lv-vol2          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
Host: scsi34 Channel: 00 Id: 00 Lun: 02
  Vendor: LIO-ORG  Model: lv-vol3          Rev: 4.0
  Type:   Direct-Access                    ANSI  SCSI revision: 05
[root@CentOS7-client ~]#

[root@CentOS7-client ~]# fdisk -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009fcf9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

Disk /dev/mapper/centos-root: 18.2 GB, 18249416704 bytes, 35643392 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdb: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 4194304 bytes


Disk /dev/sdc: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 4194304 bytes


Disk /dev/sdd: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 4194304 bytes

[root@CentOS7-client ~]#
```

* iscsi logout  
```no-highlight
[root@CentOS7-client ~]# iscsiadm --mode node --targetname iqn.2018-10.com.example:target2 --portal 192.168.0.40 -u
Logging out of session [sid: 2, target: iqn.2018-10.com.example:target2, portal: 192.168.0.40,3260]
Logout of [sid: 2, target: iqn.2018-10.com.example:target2, portal: 192.168.0.40,3260] successful.
[root@CentOS7-client ~]#  iscsiadm --mode node --targetname iqn.2018-10.com.example:target2 --portal 192.168.0.40 -o delete
```


* 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --add-service=iscsi-target --permanent
[root@centos7 ~]# firewall-cmd --reload
```







<br></br>