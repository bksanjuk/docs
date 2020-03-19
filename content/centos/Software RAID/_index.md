+++
title = "Software RAID"
weight = 20
+++


## Software RAID

{{% notice info %}}
mdadm 소개  
Centos 에서 software RAID 구성시 mdadm 을 설치하여 사용 합니다.  
[raid.wiki.kernerl.org](https://raid.wiki.kernel.org/index.php/RAID_setup) 에서 조금더 많은 정보를 확인 할수 있습니다.  
{{% /notice %}}

{{% notice note %}}
mdadm Test note"  
mdadm 을 테스트 하기 위해서는 hdd Disk 가 2개 붙어 있어야 합니다.  
본문서는 RAID 1 만 작성 하였습니다.  
{{% /notice %}}

* mdadm 설치  
```no-highlight
[root@centos7 ~]# yum install -y mdadm
```

* disk 확인  
```no-highlight
[root@centos7 ~]# fdisk -l

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

Disk /dev/sdc: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdb: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

[root@centos7 ~]#
```

* RAID 장치 활성화 확인  
```no-highlight
[root@centos7 ~]# cat /proc/mdstat
Personalities :
unused devices: <none>
[root@centos7 ~]#
```

* fdisk 작업  
filesystem type 를 Linux raid autodetect 로 설정 합니다.  
```no-highlight
[root@centos7 ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x5890a0c8.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-2097151, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151):
Using default value 2097151
Partition 1 of type Linux and of size 1023 MiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@centos7 ~]# fdisk /dev/sdc
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x5c112dd6.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-2097151, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151):
Using default value 2097151
Partition 1 of type Linux and of size 1023 MiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@centos7 ~]#
```


* mdadm 을 이용하여 sdb1 sdc1 을 RAID 1 으로 구성 합니다.  
```no-highlight
[root@centos7 ~]# mdadm --create /dev/md1 --level=1 --raid-device=2 /dev/sdb1 /dev/sdc1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
[root@centos7 ~]#
```

* mdadm.conf 파일을 생성 합니다.  

{{% notice info %}}
mdadm.conf 참고 사항  
mdadm.conf 파일에 md1 값이 없을 경우 다른 Device 로 잡힐수 있습니다.  
{{% /notice %}}

```no-highlight
[root@centos7 ~]# mdadm --detail --scan > /etc/mdadm.conf
[root@centos7 ~]# cat /etc/mdadm.conf
ARRAY /dev/md1 metadata=1.2 name=centos7:1 UUID=a7be8a5e:0cfb01ea:c55ea0af:6bd107a0
[root@centos7 ~]#
```


* /dev/md1 Device 를 포맷합니다.  

```no-highlight
[root@centos7 ~]# mkfs.xfs /dev/md1
meta-data=/dev/md1               isize=512    agcount=4, agsize=65408 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=261632, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=855, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@centos7 ~]#
```

* mount 포인트 생성 및 /etc/fstab 수정  

```no-highlight
[root@centos7 ~]# mkdir /data
[root@centos7 ~]# vi /etc/fstab
/dev/md1                                  /data                   xfs     defaults        0 0

[root@centos7 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        16G  2.1G   14G  13% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G   12M  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1       2.0G  190M  1.9G  10% /boot
tmpfs           378M     0  378M   0% /run/user/0
/dev/md1       1019M   33M  987M   4% /data
[root@centos7 ~]#
```

* system rebooting 후 확인  

```no-highlight
[root@centos7 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        16G  2.1G   14G  13% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G   12M  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1       2.0G  190M  1.9G  10% /boot
/dev/md1       1019M   33M  987M   4% /data
tmpfs           378M     0  378M   0% /run/user/0
[root@centos7 ~]# mdadm --detail --scan
ARRAY /dev/md1 metadata=1.2 name=centos7:1 UUID=a7be8a5e:0cfb01ea:c55ea0af:6bd107a0

[root@centos7 ~]# mdadm --detail /dev/md1
/dev/md1:
           Version : 1.2
     Creation Time : Wed Oct 17 23:18:02 2018
        Raid Level : raid1
        Array Size : 1046528 (1022.00 MiB 1071.64 MB)
     Used Dev Size : 1046528 (1022.00 MiB 1071.64 MB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Wed Oct 17 23:27:29 2018
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : resync

              Name : centos7:1  (local to host centos7)
              UUID : a7be8a5e:0cfb01ea:c55ea0af:6bd107a0
            Events : 19

    Number   Major   Minor   RaidDevice State
       0       8       17        0      active sync   /dev/sdb1
       1       8       33        1      active sync   /dev/sdc1
[root@centos7 ~]#
```

* RAID 상태를 확인 합니다.  
```no-highlight
[root@centos7 ~]# cat /proc/mdstat
Personalities : [raid1]
md1 : active raid1 sdb1[0] sdc1[1]
      1046528 blocks super 1.2 [2/2] [UU]

unused devices: <none>
[root@centos7 ~]#
```

{{% notice tip %}}
RAID 장애 처리  
disk 를 하나 빼고 부팅 후 raid 상태를 확인 합니다.  
{{% /notice %}}

* RAID 상태 확인  
정상적인 경우 UU 라고 나오지만 RAID 장애가 발생 하였을시 U_ 표시가 나옵니다.  
```no-highlight
[root@centos7 ~]# cat /proc/mdstat
Personalities : [raid1]
md1 : active raid1 sdb1[0]
      1046528 blocks super 1.2 [2/1] [U_]

unused devices: <none>
[root@centos7 ~]#
```

* mount 상태 확인및 Disk 쓰기 확인  
마운트 상태 확인 및 touch 명령어를 이용하여 빈파일을 생성합니다.  
```no-highlight
[root@centos7 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        16G  2.1G   14G  13% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G   12M  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1       2.0G  190M  1.9G  10% /boot
/dev/md1       1019M   33M  987M   4% /data
[root@centos7 ~]# cd /data
[root@centos7 data]# touch 0
```

* 디스크를 추가 하고 Fdisk 작업을 합니다.  

```no-highlight
[root@centos7 ~]# fdisk -l

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

Disk /dev/sdc: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdb: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x5890a0c8

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2097151     1047552   fd  Linux raid autodetect

Disk /dev/md1: 1071 MB, 1071644672 bytes, 2093056 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

[root@centos7 ~]# fdisk /dev/sdc
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x50182188.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-2097151, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-2097151, default 2097151):
Using default value 2097151
Partition 1 of type Linux and of size 1023 MiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@centos7 ~]#
```

* Disk 를 /dev/md1 에 추가 합니다.  
디스크 추가시 /proc/mdstat 파일을 확인 하면 recovery 상황을 볼수 있습니다.  
```no-highlight
[root@centos7 ~]# mdadm /dev/md1 --add /dev/sdc1
mdadm: added /dev/sdc1
[root@centos7 ~]# cat /proc/mdstat
Personalities : [raid1]
md1 : active raid1 sdc1[2] sdb1[0]
      1046528 blocks super 1.2 [2/1] [U_]
      [===============>.....]  recovery = 76.4% (800896/1046528) finish=0.0min speed=266965K/sec

unused devices: <none>
[root@centos7 ~]#
```

*  recovery 후 확인  
```no-highlight
[root@centos7 ~]# cat /proc/mdstat
Personalities : [raid1]
md1 : active raid1 sdc1[2] sdb1[0]
      1046528 blocks super 1.2 [2/2] [UU]

unused devices: <none>
[root@centos7 ~]#
```

* RAID 삭제 방법  
/etc/fstab 의 mount 정보도 삭제 합니다.  
```no-highlight
[root@centos7 ~]# umount /data
[root@centos7 ~]# mdadm -S /dev/md1
mdadm: stopped /dev/md1
[root@centos7 ~]# mdadm --zero-superblock /dev/sdb1
[root@centos7 ~]# mdadm --zero-superblock /dev/sdc1
[root@centos7 ~]# cat /proc/mdstat
Personalities : [raid1]
unused devices: <none>
[root@centos7 ~]#
```