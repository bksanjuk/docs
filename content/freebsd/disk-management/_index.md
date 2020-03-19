+++
title = "Disk Management"
weight = 7
+++


## Disk 관리

{{% notice note %}}
UFS 기준으로 작성 하였습니다.  
ZFS 의 경우 차후 별도로 작성하도록 하겠습니다.  
{{% /notice %}}


```no-highlight
/dev/ 디렉토리를 확인하여 신규로 추가된 하드디스크를 확인 합니다.  
root@BSD11:~ # ls -al /dev/da*
crw-r-----  1 root  operator  0x4e Sep 23 06:20 /dev/da0
crw-r-----  1 root  operator  0x54 Sep 23 06:20 /dev/da0s1
crw-r-----  1 root  operator  0x56 Sep 23 06:20 /dev/da0s1a
crw-r-----  1 root  operator  0x57 Sep 23 06:20 /dev/da0s1b
crw-r-----  1 root  operator  0x49 Sep 23 06:24 /dev/da1
root@BSD11:~ #  

추가된 디스크 사이즈 확인
root@BSD11:~ # diskinfo -v da1
da1
        512             # sectorsize
        10737418240     # mediasize in bytes (10G)
        20971520        # mediasize in sectors
        0               # stripesize
        0               # stripeoffset
        1305            # Cylinders according to firmware.
        255             # Heads according to firmware.
        63              # Sectors according to firmware.
                        # Disk ident.
        Not_Zoned       # Zone Mode

root@BSD11:~ #

기존디스크 확인
root@BSD11:~ # gpart show
=>      63  41942977  da0  MBR  (20G)
        63         1       - free -  (512B)
        64  41942975    1  freebsd  [active]  (20G)
  41943039         1       - free -  (512B)

=>       0  41942975  da0s1  BSD  (20G)
         0  39845888      1  freebsd-ufs  (19G)
  39845888   2097086      2  freebsd-swap  (1.0G)
  41942974         1         - free -  (512B)

root@BSD11:~ #

```
<br></br>

> da1 Device gpt 파티션 설정  

```no-highlight
신규로 추가된 da1 Device 를 gpt 파티션 테이블로 설정 합니다.  
root@BSD11:~ # gpart create -s GPT da1
da1 created
root@BSD11:~ # gpart show da1
=>      40  20971440  da1  GPT  (10G)
        40  20971440       - free -  (10G)

root@BSD11:~ #
```
<br></br>

> 용량지정 없이 모든 용량을 지정할경우  

```no-highlight
root@BSD11:~ # gpart add -t freebsd-ufs da1
da1p1 added
root@BSD11:~ # gpart show da1
=>      40  20971440  da1  GPT  (10G)
        40  20971440    1  freebsd-ufs  (10G)

root@BSD11:~ #

** 별도의 용량을 지정할 경우 **  
root@BSD11:~ # gpart add -t freebsd-ufs -s 5G da1
```
<br></br>

> File System 포멧  

```no-highlight
newfs 명령어로 파일시스템을 포멧 합니다. 
oot@BSD11:~ # newfs -U /dev/da1p1
/dev/da1p1: 10240.0MB (20971440 sectors) block size 32768, fragment size 4096
        using 17 cylinder groups of 626.09MB, 20035 blks, 80256 inodes.
        with soft updates
super-block backups (for fsck_ffs -b #) at:
 192, 1282432, 2564672, 3846912, 5129152, 6411392, 7693632, 8975872, 10258112, 11540352, 12822592, 14104832, 15387072, 16669312, 17951552, 19233792, 20516032
root@BSD11:~ #
```
<br></br>

> 마운트 포인트 생성 및 /etc/fstab 등록  

```no-highlight
root@BSD11:~ # mkdir /data
root@BSD11:~ # vi /etc/fstab
/dev/da1p1      /data           ufs     rw      0       0

마운트 확인
root@BSD11:~ # df -h
Filesystem     Size    Used   Avail Capacity  Mounted on
/dev/da0s1a     18G    2.7G     14G    16%    /
devfs          1.0K    1.0K      0B   100%    /dev
/dev/da1p1     9.7G    8.0K    8.9G     0%    /data
root@BSD11:~ #
```
<br></br>

> Disk 제거방법  

```no-highlight
root@BSD11:~ # umount /data
root@BSD11:~ # vi /etc/fstab
/dev/da1p1      /data           ufs     rw      0       0  // 라인제거

Disk 확인
root@BSD11:~ # gpart show
=>      63  41942977  da0  MBR  (20G)
        63         1       - free -  (512B)
        64  41942975    1  freebsd  [active]  (20G)
  41943039         1       - free -  (512B)

=>       0  41942975  da0s1  BSD  (20G)
         0  39845888      1  freebsd-ufs  (19G)
  39845888   2097086      2  freebsd-swap  (1.0G)
  41942974         1         - free -  (512B)

=>      40  20971440  da1  GPT  (10G)
        40  20971440    1  freebsd-ufs  (10G)

root@BSD11:~ #

da1  GPT  (10G) 디스크를 삭제 합니다. 

root@BSD11:~ # gpart delete -i1 da1
da1p1 deleted
root@BSD11:~ # gpart destroy -F da1
da1 destroyed
root@BSD11:~ #

```
<br></br>
