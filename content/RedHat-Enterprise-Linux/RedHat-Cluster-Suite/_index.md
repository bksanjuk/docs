+++
title = "RedHat Cluster Suite"
weight = 1
+++

{{% notice warning %}}
작성중인 문서 입니다.  
KVM 내용은 차후 작성할 예정 입니다.  
{{% /notice %}}


> 2019-05-21-vmware-fence-rhcs-install.md  
> 본 문서는 vmware ESXi 5.5 와 vcenter 로 테스트 하였습니다.  
> OS Version 은 rhel 7.6 입니다.  
> pcmk_reboot_action fence option 사용시 vmware 에서는 정상적으로 작동하지 않습니다.  


## OS 설정  
> 방화벽 및 selinux 를 disable 합니다.  
> ha-node01 , ha-node02 에서 작업  
```no-highlight
# systemctl disable firewalld


# echo 'GRUB_CMDLINE_LINUX="net.ifnames=0"' >>/etc/default/grub
# grub2-mkconfig -o /boot/grub2/grub.cfg
```

> hosts 파일 설정  
> ha-node01 , ha-node02 에서 작업  
```no-highlight
[root@ha-node01 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

10.10.10.32     ha-node01
10.10.10.33     ha-node02
```

> iso.repo 파일 생성  
> ha-node01 , ha-node02 에서 작업  

```no-highlight
[root@ha-node01 ~]# vi /etc/yum.repos.d/iso.repo

[iso]
name=iso
baseurl=file:///mnt
gpgcheck=0

[HighAvailability]
name=HighAvailability
baseurl=file:///mnt/addons/HighAvailability
gpgcheck=0

[ResilientStorage]
name=RS
baseurl=file:///mnt/addons/ResilientStorage
gpgcheck=0
```


> network 설정  

```no-highlight
[root@ha-node01 ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0

TYPE=Ethernet
BOOTPROTO=none
DEVICE=eth0
ONBOOT=yes
IPADDR=10.10.10.32
NETMASK=255.255.255.0

[root@ha-node02 ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0
TYPE=Ethernet
BOOTPROTO=none
DEVICE=eth0
ONBOOT=yes
IPADDR=10.10.10.33
NETMASK=255.255.255.0
```


## 패키지 설치  

> High-availability 패키지 설치  

```no-highlight
[root@ha-node01 ~]# yum install -y pcs fence-agents-all
[root@ha-node02 ~]# yum install -y pcs fence-agents-all


high-availability 패키지 설치시 hacluster user 가 생성 됩니다. 
hacluster 유저의 패스워드를 ha-node01 , ha-node02 동일하게 설정 합니다. 

[root@ha-node01 ~]# passwd hacluster
[root@ha-node02 ~]# passwd hacluster


ha-node01 , ha-node02 에 동일하게 설정 합니다. 

[root@ha-node01 ~]# systemctl enable pcsd
[root@ha-node01 ~]# systemctl start pcsd
```

> ha-node01 에서만 인증을 실행 합니다.  

```no-highlight
root@ha-node01 ~]# pcs cluster auth ha-node01 ha-node02
Username: hacluster
Password:
ha-node02: Authorized
ha-node01: Authorized
[root@ha-node01 ~]#

```

## 클러스터 생성  

> 클러스터를 만들고 노드를 추가한후 Cluster 서비스를 시작 합니다.  
> ha-node01 에서만 실행  

```no-highlight
[root@ha-node01 ~]# pcs cluster setup --name ha-scv ha-node01 ha-node02
Destroying cluster on nodes: ha-node01, ha-node02...
ha-node02: Stopping Cluster (pacemaker)...
ha-node01: Stopping Cluster (pacemaker)...
ha-node02: Successfully destroyed cluster
ha-node01: Successfully destroyed cluster

Sending 'pacemaker_remote authkey' to 'ha-node01', 'ha-node02'
ha-node01: successful distribution of the file 'pacemaker_remote authkey'
ha-node02: successful distribution of the file 'pacemaker_remote authkey'
Sending cluster config files to the nodes...
ha-node01: Succeeded
ha-node02: Succeeded

Synchronizing pcsd certificates on nodes ha-node01, ha-node02...
ha-node02: Success
ha-node01: Success
Restarting pcsd on the nodes in order to reload the certificates...
ha-node02: Success
ha-node01: Success
[root@ha-node01 ~]#
```

> Cluster 서비스가 부팅후 자동으로 실행 될수 있도록 enable 합니다.  
> ha-node01 에서만 실행  
```no-highligh
[root@ha-node01 ~]# pcs cluster enable --all
```



## pcs cluster 를 실행  
> ha-node01 , ha-node02 에서 실행  

```no-highlight
[root@ha-node01 ~]# pcs cluster start
[root@ha-node02 ~]# pcs cluster start


pcs cluster 상태를 확인 합니다. 
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
No stonith devices and stonith-enabled is not false

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Thu May  9 01:37:03 2019
Last change: Thu May  9 01:36:44 2019 by hacluster via crmd on ha-node01

2 nodes configured
0 resources configured

Online: [ ha-node01 ha-node02 ]

No resources


Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled

  pcsd: active/enabled
[root@ha-node01 ~]#
```


## Fence Device 설정 
> vmware 환경 , vcenter 로 로그인 해야 합니다  
> ha-node01 에서 작업  

```no-highlight
vm node 를 검색 합니다.  
 # fence_vmware_soap -a 10.10.10.201 -l administrator@vsphere.local -p Dkdlxlqmfpdls1! -z --ssl-insecure -o list |grep -i ha-node


ha-node01 / ha-node02 fence-device 를 생성 합니다.  

ha-node01
action="reboot" 와 pcmk_reboot_action 차이는 ? 

[root@ha-node01 ~]# pcs stonith create fence_node01 fence_vmware_soap pcmk_host_list="ha-node01" port="ha-node01" ipaddr="10.10.10.201" ssl_insecure=1 login="administrator@vsphere.local" passwd='Dkdlxlqmfpdls1!'  ssl_insecure=1 action="reboot" pcmk_monitor_timeout=60s --force

[root@ha-node01 ~]# pcs stonith create fence_node02 fence_vmware_soap pcmk_host_list="ha-node02" port="ha-node02" ipaddr="10.10.10.201" ssl_insecure=1 login="administrator@vsphere.local" passwd='Dkdlxlqmfpdls1!'  ssl_insecure=1 action="reboot" pcmk_monitor_timeout=60s --force


[root@ha-node01 ~]# pcs property set stonith-timeout=120s
[root@ha-node01 ~]# pcs constraint location fence_node02 prefers ha-node02
[root@ha-node01 ~]# pcs resource cleanup --all
Cleaned up fence_node02 on ha-node02
Cleaned up fence_node02 on ha-node01
Waiting for 1 replies from the CRMd. OK
[root@ha-node01 ~]#

```

## Resource 설정  

> vip 설정  


```no-highlight
[root@ha-node01 ~]# pcs resource create VIP IPaddr2 ip=10.10.10.40 cidr_netmask=22 nic=eth0 --group test-svc
```

> 모니터링 설정  
```no-highlight
[root@ha-node01 ~]# pcs resource create svcnet-monitor ethmonitor interface=eth0 --clone
[root@ha-node01 ~]# pcs constraint location VIP rule score=-INFINITY ethmonitor-eth0 ne 1
```

> Cluster 상태 확인  

```no-highlight
[root@ha-node01 ~]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:9f:79:e7 brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.35/24 brd 10.10.10.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet 10.10.10.40/22 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe9f:79e7/64 scope link
       valid_lft forever preferred_lft forever
[root@ha-node01 ~]#
```


> pcs resource 삭제  


```no-highlight
[root@ha-node01 ~]# pcs resource delete  fence_node01
```

> Resource Group 이관  

```no-highlight
[root@ha-node02 ~]# pcs status 

~중략
 fence_node01   (stonith:fence_vmware_soap):    Started ha-node02
 fence_node02   (stonith:fence_vmware_soap):    Starting ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node02

Resource Group 을 이관 합니다. 
ha-node01 -> ha-node02 로 이관
[root@ha-node02 ~]# pcs resource move test-svc ha-node01

Resource Group 상태 확인  
[root@ha-node02 ~]# pcs status
~중략 
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

```

## fencing test  
> ha-node02 에서 ha-node01 시스템을 리부팅 시킵니다.  
> https://access.redhat.com/solutions/1172133  
> https://access.redhat.com/solutions/3994601  



```no-highlight

[root@ha-node02 ~]#  stonith_admin --reboot ha-node01
약 120s 정도의 시간이 걸립니다. 

Cluster 상태를 확인 합니다. 
[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node02 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Fri May 17 00:01:18 2019
Last change: Thu May 16 23:56:28 2019 by root via crm_resource on ha-node02

2 nodes configured
5 resources configured

Online: [ ha-node02 ]
OFFLINE: [ ha-node01 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Started ha-node02
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node02
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node02 ]
     Stopped: [ ha-node01 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node02 ~]#



오류 메시지 삭제후 Cluster 상태를 확인 합니다. 

[root@ha-node02 ~]# pcs resource cleanup --all
Cleaned up all resources on all nodes
[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node02 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Fri May 17 00:02:07 2019
Last change: Thu May 16 23:56:28 2019 by root via crm_resource on ha-node02

2 nodes configured
5 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Started ha-node02
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node02 ~]#

```

> Nic 장애 테스트  
> 장애 시나리오: ha-node01 의 eth0 Nic Down 현상 시 ha-node01 이 정상적으로 시스템 리부팅이 되는지 확인 합니다.  
> 서비스 상태 확인 후 ha-node01 의 eth0 Nic 를 Down 합니다.  
> Network 이 단절된 관계로 vmware local(물리장비) 에서 확인 합니다.  

```no-highlight

[root@ha-node01 ~]# pcs status
~중략 
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]
[root@ha-node01 ~]# ifdown eth0

[root@ha-node01 ~]# pcs status
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01 (UNCLEAN)           <--- 상태가 UNCLEAN 으로 변경 되었습니다.
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     svcnet-monitor     (ocf::heartbeat:ethmonitor):    Started ha-node01 (UNCLEAN)   <--- 상태가 UNCLEAN 으로 변경 되었습니다. 
     Started: [ ha-node02 ]


ha-node02 확인 

[root@ha-node02 ~]# pcs status
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Starting ha-node02   <-- ha-node01 에서 ha-node02 로 VIP 가 이관 되었습니다.  
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node02 ]
     Stopped: [ ha-node01 ]

```


## HA-LVM  

> ha-storage vm 을 설정 합니다.  
> 운영 장비에서는 Storage Data 보호를 위하여 HA-LVM 을 사용 해야 합니다.  
> Resource 를 사용 하는 node 에서 Storage mount 시 다른 한쪽에서는 mount 할수 없습니다.  
> 동일볼륨을 모든 node 에서 마운트 하였을시 filesystem 이 깨지는것을 방지 하기 위하여 사용 합니다.  

- /etc/hosts 설정 
> ha-node01 , ha-node02 , ha-storage 에 동일하게 추가 합니다. 

```no-highlight
[root@ha-storage ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

10.10.10.35     ha-node01
10.10.10.36     ha-node02
10.10.10.37     ha-storage

/dev/sdb 10G 의 Disk 를 VM에 추가 하였습니다. 
[root@ha-storage ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0      2:0    1    4K  0 disk
sda      8:0    0   16G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0   14G  0 part /
sdb      8:16   0   10G  0 disk
```

> 패키지 설치  

```no-highlight
[root@ha-storage ~]# yum install targetcli
```


> fdisk 작업  

```no-highlight
[root@ha-storage ~]# fdisk /dev/sdb

iscsi 용 볼륨을 생성 합니다. 
용량은 전체 용량을 설정 하였습니다. 

Select (default p): p
Partition number (1-4, default 1):
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
[root@ha-storage ~]# partprobe
Warning: Unable to open /dev/sr0 read-write (Read-only file system).  /das been opened read-only.
[root@ha-storage ~]#
```

> LVM 패키지를 설치 및 LVM 을 생성 합니다.  

```no-highlight
[root@ha-storage ~]# yum install -y lvm2

[root@ha-storage ~]# vgcreate vg00 /dev/sdb1
  Volume group "vg00" successfully created
[root@ha-storage ~]# lvcreate -L 1G -n lv-vol1 vg00
  Logical volume "lv-vol1" created.
[root@ha-storage ~]# lvcreate -L 1G -n lv-vol2 vg00
  Logical volume "lv-vol2" created.
[root@ha-storage ~]# lvcreate -L 1G -n lv-vol3 vg00
  Logical volume "lv-vol3" created.
[root@ha-storage ~]#
[root@ha-storage ~]# lvs
  LV      VG   Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv-vol1 vg00 -wi-a----- 1.00g
  lv-vol2 vg00 -wi-a----- 1.00g
  lv-vol3 vg00 -wi-a----- 1.00g
[root@ha-storage ~]#
```

> iscsi block 생성  


```no-highlight
[root@ha-storage ~]# targetcli
/iscsi/iqn.20...:target1/tpg1> cd /backstores/block
/backstores/block> create lv-vol1 /dev/vg00/lv-vol1
/backstores/block> create lv-vol2 /dev/vg00/lv-vol2
/backstores/block> create lv-vol3 /dev/vg00/lv-vol3
/backstores/block> cd /iscsi
/iscsi> create iqn.2019-05.com.hacluster:target1
/iscsi> cd iqn.2019-05.com.hacluster:target1/
/iscsi/iqn.20...uster:target1> cd tpg1/luns
/iscsi/iqn.20...et1/tpg1/luns> create /backstores/block/lv-vol1
/iscsi/iqn.20...et1/tpg1/luns> create /backstores/block/lv-vol2
/iscsi/iqn.20...et1/tpg1/luns> create /backstores/block/lv-vol3


/iscsi/iqn.20...:target1/tpg1> set attribute authentication=0
/iscsi/iqn.20...:target1/tpg1> set attribute demo_mode_write_protect=0
/iscsi/iqn.20...:target1/tpg1> set attribute generate_node_acls=1
/iscsi/iqn.20...:target1/tpg1> exit
```


> ha-node01 / ha-node01 iscsi 연결  
> iscsi-initiator-utils 패키지를 설치 합니다.  

```no-highlight
[root@ha-node01 ~]# yum install -y iscsi-initiator-utils
[root@ha-node02 ~]# yum install -y iscsi-initiator-utils
```



> ha-node01 에서 ha-storage 볼륨을 확인 합니다.  

```no-highlight
[root@ha-node01 ~]# iscsiadm --mode discoverydb --type sendtargets --portal ha-storage --discover
10.10.10.37:3260,1 iqn.2019-05.com.hacluster:target1
```

> ha-node02 에서 ha-storage 볼륨을 확인 합니다.  

```no-highlight
[root@ha-node02 ~]# iscsiadm --mode discoverydb --type sendtargets --portal ha-storage --discover
10.10.10.37:3260,1 iqn.2019-05.com.hacluster:target1
```


> iscsi server 에 로그인 합니다.  

```no-highlight
[root@ha-node01 ~]# iscsiadm --mode node --targetname iqn.2019-05.com.hacluster:target1 --portal ha-storage --login

[root@ha-node02 ~]# iscsiadm --mode node --targetname iqn.2019-05.com.hacluster:target1 --portal ha-storage --login


[root@ha-node01 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0      2:0    1    4K  0 disk
sda      8:0    0   16G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0   14G  0 part /
sdb      8:16   0    1G  0 disk         <---   iscsi 볼륨
sdc      8:32   0    1G  0 disk         <---   iscsi 볼륨
sdd      8:48   0    1G  0 disk         <---   iscsi 볼륨
[root@ha-node01 ~]#


[root@ha-node02 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0      2:0    1    4K  0 disk
sda      8:0    0   16G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0   14G  0 part /
sdb      8:16   0    1G  0 disk       <---   iscsi 볼륨
sdc      8:32   0    1G  0 disk       <---   iscsi 볼륨
sdd      8:48   0    1G  0 disk       <---   iscsi 볼륨
[root@ha-node02 ~]#
```

> iscsi Volume 정보 확인  
> ha-storage 시스템 에서 확인 가능 합니다.  

```no-highlight
[root@ha-storage ~]# targetcli
targetcli shell version 2.1.fb41
Copyright 2011-2013 by Datera, Inc and others.
For help on commands, type 'help'.

/iscsi/iqn.20...:target2/tpg1> ls
o- tpg1 ............................................................ [gen-acls, no-auth]
  o- acls .................................................................... [ACLs: 0]
  o- luns .................................................................... [LUNs: 3]
  | o- lun0 ........................................ [block/lv-vol1 (/dev/vg00/lv-vol1)]
  | o- lun1 ........................................ [block/lv-vol2 (/dev/vg00/lv-vol2)]
  | o- lun2 ........................................ [block/lv-vol3 (/dev/vg00/lv-vol3)]
  o- portals .............................................................. [Portals: 1]
    o- 0.0.0.0:3260 ............................................................... [OK]
/iscsi/iqn.20...:target2/tpg1> cd

/> ls
o- / ............................................................................. [...]
  o- backstores .................................................................. [...]
  | o- block ...................................................... [Storage Objects: 3]
  | | o- lv-vol1 ...................... [/dev/vg00/lv-vol1 (2.0GiB) write-thru activated]
  | | o- lv-vol2 ...................... [/dev/vg00/lv-vol2 (2.0GiB) write-thru activated]
  | | o- lv-vol3 ...................... [/dev/vg00/lv-vol3 (2.0GiB) write-thru activated]
  | o- fileio ...................................................... [Storage Objects: 1]
  | | o- disk1 ....................... [/images/disk1.img (10.0GiB) write-back activated]
  | o- pscsi ....................................................... [Storage Objects: 0]
  | o- ramdisk ..................................................... [Storage Objects: 0]
  o- iscsi ................................................................. [Targets: 2]
  | o- iqn.2018-04.com.example:target1 ........................................ [TPGs: 1]
  | | o- tpg1 ....................................................... [gen-acls, no-auth]
  | |   o- acls ............................................................... [ACLs: 0]
  | |   o- luns ............................................................... [LUNs: 1]
  | |   | o- lun0 .................................... [fileio/disk1 (/images/disk1.img)]
  | |   o- portals ......................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 .......................................................... [OK]
  | o- iqn.2018-04.com.example:target2 ........................................ [TPGs: 1]
  |   o- tpg1 ....................................................... [gen-acls, no-auth]
  |     o- acls ............................................................... [ACLs: 0]
  |     o- luns ............................................................... [LUNs: 3]
  |     | o- lun0 ................................... [block/lv-vol1 (/dev/vg00/lv-vol1)]
  |     | o- lun1 ................................... [block/lv-vol2 (/dev/vg00/lv-vol2)]
  |     | o- lun2 ................................... [block/lv-vol3 (/dev/vg00/lv-vol3)]
  |     o- portals ......................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 .......................................................... [OK]
  o- loopback .............................................................. [Targets: 0]
/> ls |grep -i target
o- / .............................................................................. [...]
  o- backstores ................................................................... [...]
  | o- block ....................................................... [Storage Objects: 3]
  | | o- lv-vol1 ...................... [/dev/vg00/lv-vol1 (2.0GiB) write-thru activated]
  | | o- lv-vol2 ...................... [/dev/vg00/lv-vol2 (2.0GiB) write-thru activated]
  | | o- lv-vol3 ...................... [/dev/vg00/lv-vol3 (2.0GiB) write-thru activated]
  | o- fileio ...................................................... [Storage Objects: 1]
  | | o- disk1 ....................... [/images/disk1.img (10.0GiB) write-back activated]
  | o- pscsi ....................................................... [Storage Objects: 0]
  | o- ramdisk ..................................................... [Storage Objects: 0]
  o- iscsi ................................................................. [Targets: 2]
  | o- iqn.2018-04.com.example:target1 ........................................ [TPGs: 1]
  | | o- tpg1 ....................................................... [gen-acls, no-auth]
  | |   o- acls ............................................................... [ACLs: 0]
  | |   o- luns ............................................................... [LUNs: 1]
  | |   | o- lun0 .................................... [fileio/disk1 (/images/disk1.img)]
  | |   o- portals ......................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 .......................................................... [OK]
  | o- iqn.2018-04.com.example:target2 ........................................ [TPGs: 1]
  |   o- tpg1 ....................................................... [gen-acls, no-auth]
  |     o- acls ............................................................... [ACLs: 0]
  |     o- luns ............................................................... [LUNs: 3]
  |     | o- lun0 ................................... [block/lv-vol1 (/dev/vg00/lv-vol1)]
  |     | o- lun1 ................................... [block/lv-vol2 (/dev/vg00/lv-vol2)]
  |     | o- lun2 ................................... [block/lv-vol3 (/dev/vg00/lv-vol3)]
  |     o- portals ......................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 .......................................................... [OK]
  o- loopback .............................................................. [Targets: 0]
/>
```



## lvm2 패키지 설치  
> ha-node01 , ha-node02 에 설치 합니다.  
> cluster 에서 lvm 사용시 설정 하며 lvm 을 설정할 경우 clvm 을 이용한 halvm 을 구성 해야 합니다.  

```no-highlight
[root@ha-node01 ~]# yum install -y lvm2
[root@ha-node02 ~]# yum install -y lvm2
```

- lvm 생성  
> ha-node01 에서 작업 합니다. (한쪽 노드에서만 작업을 합니다.)  

```no-highlight
[root@ha-node01 ~]# pvcreate /dev/sdb /dev/sdc /dev/sdd
[root@ha-node01 ~]# vgcreate vg00 /dev/sdb /dev/sdc /dev/sdd
[root@ha-node01 ~]# lvcreate -l 100%free -n halvm vg00


lvm 정보를 확인 합니다. 
[root@ha-node01 ~]# lvs
  LV    VG   Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  halvm vg00 -wi-a----- <2.91g
[root@ha-node01 ~]#
```

> HA-LVM Resource 추가  
> ha-node01 , ha-node02 에서 작업  
> lvmconf 실행시 Warning 메세지는 무시 합니다.  

```no-highlight
[root@ha-node01 ~]# systemctl stop lvm2-lvmetad
[root@ha-node01 ~]# systemctl disable lvm2-lvmetad
[root@ha-node01 ~]# systemctl stop lvm2-lvmetad.socket
[root@ha-node01 ~]# systemctl disable lvm2-lvmetad.socket



[root@ha-node01 ~]# lvmconf --enable-halvm --services --startstopservices
Warning: Stopping lvm2-lvmetad.service, but it can still be activated by:
  lvm2-lvmetad.socket
Removed symlink /etc/systemd/system/sysinit.target.wants/lvm2-lvmetad.socket.
[root@ha-node01 ~]#

[root@ha-node02 ~]# lvmconf --enable-halvm --services --startstopservices
Warning: Stopping lvm2-lvmetad.service, but it can still be activated by:
  lvm2-lvmetad.socket
Removed symlink /etc/systemd/system/sysinit.target.wants/lvm2-lvmetad.socket.
[root@ha-node02 ~]#

- lvm.conf 확인 

[root@ha-node01 ~]# grep -e "locking_type = 1" -e "use_lvmetad = 0" /etc/lvm/lvm.conf
    locking_type = 1
    use_lvmetad = 0
[root@ha-node01 ~]#

[root@ha-node02 ~]# grep -e "locking_type = 1" -e "use_lvmetad = 0" /etc/lvm/lvm.conf
    locking_type = 1
    use_lvmetad = 0
[root@ha-node02 ~]#

- lvm2-lvmetad 서비스 확인 

[root@ha-node01 ~]# systemctl status lvm2-lvmetad.service | grep -i -e active -e loaded
   Loaded: loaded (/usr/lib/systemd/system/lvm2-lvmetad.service; static; vendor preset: enabled)
   Active: inactive (dead) since Fri 2019-05-17 23:08:31 KST; 1min 28s ago
[root@ha-node01 ~]# 


[root@ha-node02 ~]# systemctl status lvm2-lvmetad.service | grep -i -e active -e loaded
   Loaded: loaded (/usr/lib/systemd/system/lvm2-lvmetad.service; static; vendor preset: enabled)
   Active: inactive (dead) since Fri 2019-05-17 23:08:31 KST; 1min 28s ago
[root@ha-node01 ~]# 
```


> OS 에서 항상 사용중인 LVM 이 있을경우 volume_list 에 추가 합니다.  
> halvm resource 를 설정 하면 안됩니다.  
 
- ha-node01 , ha-node02 에서 작업  

```no-highlight
[root@ha-node01 ~]# vi /etc/lvm/lvm.conf

        # Example
        # volume_list = [ "vg1", "vg2/lvol1", "@tag1", "@*" ]
         volume_list = ["VGOS"]


만약 설정할 내용이 없을 경우 아래와 같이 설정 합니다.  
         volume_list = []

Resource 를 추가 합니다. 
ha-node01 에서 작업 

[root@ha-node01 ~]# pcs resource create halvm LVM volgrpname=vg00 exclusive=true --group test-svc


initd 를 재갱신 합니다. 
(주의!!: lvm os 구성후 lvm.conf 에 Volum_list 추가 없이 Dracut 명령어를 실행할 경우 정상적으로 os 가 부팅 되지 않습니다. 
Rescue mode 에서 lvm.conf 설정변경후 Dracut 으로 initrd 를 갱신해야 합니다.)

[root@ha-node01 ~]# dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
[root@ha-node02 ~]# dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)


system을 재시작 합니다. 
ha-node01 부터 

[root@ha-node01 ~]# init 6

[root@ha-node02 ~]# init 6

resource 를 cleanup 합니다. 
[root@ha-node02 ~]# pcs resource cleanup --all
Cleaned up all resources on all nodes
Waiting for 3 replies from the CRMd... OK

cluster 정보를 확인 합니다. 

[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Sat May 18 00:49:54 2019
Last change: Sat May 18 00:49:49 2019 by hacluster via crmd on ha-node02

2 nodes configured
6 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Starting ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node02 ~]# 
```

- halvm resource 가 정상적으로 기동이 안되는 경우  

```no-highlight
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set                                                                                                                                    'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node02 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Tue May 21 18:52:57 2019
Last change: Tue May 21 18:47:04 2019 by root via cibadmin on ha-node01

2 nodes configured
6 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Starting ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Starting ha-node01
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node02
     halvm      (ocf::heartbeat:LVM):   Stopped               <--- halvm resource 가 Stop 상태 입니다. 
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ]
     Stopped: [ ha-node02 ]

Failed Actions:
* halvm_monitor_0 on ha-node02 'unknown error' (1): call=30, status=complete, ex                                                                                                                                   itreason='WARNING: vg00 is active without the cluster tag, "pacemaker"',
    last-rc-change='Tue May 21 18:47:05 2019', queued=1ms, exec=291ms


Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node01 ~]#


[root@ha-node01 ~]# vgchange -ay /dev/vg00
  0 logical volume(s) in volume group "vg00" now active


[root@ha-node01 ~]# pcs resource cleanup --all
Cleaned up all resources on all nodes
Waiting for 2 replies from the CRMd.. OK
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set                                                                                                                                    'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Tue May 21 18:53:43 2019
Last change: Tue May 21 18:53:38 2019 by hacluster via crmd on ha-node01

2 nodes configured
6 resources configured

Online: [ ha-node01 ]
OFFLINE: [ ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Starting ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Starting ha-node01
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ]
     Stopped: [ ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node01 ~]#

```


> HA-LVM 을 Mount 하기 위하여 xfs 파일시스템 으로 포멧합니다.  
> xfs 파일시스템 포멧시 test-svc Resource 를 가지고 있는 node 에서 작업 해야 합니다.  

```no-highlight
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Sat May 18 00:52:10 2019
Last change: Sat May 18 00:49:49 2019 by hacluster via crmd on ha-node02

2 nodes configured
6 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Started ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01                    <--- ha-node01 에서 halvm Resource 를 가지고 있습니다. 
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node01 ~]#
```

> ha-node02 에서 xfs 파일시스템으로 포멧을 합니다.  

```no-highlight
[root@ha-node01 ~]# mkfs.xfs /dev/mapper/vg00-halvm -f
meta-data=/dev/mapper/vg00-halvm isize=512    agcount=4, agsize=190464 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=761856, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@ha-node01 ~]#


Mount 디렉토리 생성 
ha-node01 , ha-node02 에서 작업 합니다. 

[root@ha-node01 ~]# mkdir /test
[root@ha-node02 ~]# mkdir /test
```



> xfs 파일시스템 리소스를 추가 합니다. (ha-node01 에서만 작업 합니다.)  

```no-highlight
[root@ha-node01 ~]# pcs resource create testlv_FS Filesystem device="/dev/mapper/vg00-halvm" directory="/test" fstype="xfs" force_unmount=true --group test-svc

Cluster 확인 
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Sat May 18 00:55:17 2019
Last change: Sat May 18 00:55:08 2019 by root via cibadmin on ha-node01

2 nodes configured
7 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Started ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01
     testlv_FS  (ocf::heartbeat:Filesystem):    Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node01 ~]#
```

> mount 확인  

```no-highlight
[root@ha-node01 ~]# df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/sda3                14G  1.4G   13G  10% /
devtmpfs                990M     0  990M   0% /dev
tmpfs                  1000M   54M  946M   6% /dev/shm
tmpfs                  1000M  8.9M  991M   1% /run
tmpfs                  1000M     0 1000M   0% /sys/fs/cgroup
/dev/sda1              1014M  137M  878M  14% /boot
tmpfs                   200M     0  200M   0% /run/user/0
/dev/mapper/vg00-halvm  2.9G   33M  2.9G   2% /test   <--- /test 디렉토리에 정상적으로 Mount 되었습니다. 
[root@ha-node01 ~]#


ha-node01 에서 ha-node02 로 서비스 구룹을 이관 합니다.  
[root@ha-node01 ~]# pcs resource move test-svc ha-node02


[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Sat May 18 00:58:06 2019
Last change: Sat May 18 00:58:03 2019 by hacluster via crmd on ha-node01

2 nodes configured
7 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Started ha-node02
 fence_node02   (stonith:fence_vmware_soap):    Started ha-node02
 Resource Group: test-svc                                                      <-- 서비스 구룹 
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node02              <-- ha-node02 에서 정상적으로 실행 중입니다. 
     halvm      (ocf::heartbeat:LVM):   Started ha-node02                      <-- ha-node02 에서 정상적으로 실행 중입니다. 
     testlv_FS  (ocf::heartbeat:Filesystem):    Started ha-node02              <-- ha-node02 에서 정상적으로 실행 중입니다. 
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node02 ~]#


[root@ha-node02 ~]# df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/sda3                14G  1.3G   13G   9% /
devtmpfs                990M     0  990M   0% /dev
tmpfs                  1000M   39M  962M   4% /dev/shm
tmpfs                  1000M  8.9M  991M   1% /run
tmpfs                  1000M     0 1000M   0% /sys/fs/cgroup
/dev/sda1              1014M  137M  878M  14% /boot
tmpfs                   200M     0  200M   0% /run/user/0
/dev/mapper/vg00-halvm  2.9G   33M  2.9G   2% /test           <-- /test 디렉토리가 Mount 되었습니다. 
[root@ha-node02 ~]#
```


## HALVM 구성  

> ha-node01 , ha-node02 에서 작업 합니다.  
> Resilient Storage package group 을 설치 합니다.  
> iscsi 에 login 합니다.  

```no-highlight
[root@ha-node01 ~]# yum groupinstall 'Resilient Storage' -y 
[root@ha-node01 ~]# yum install iscsi-init* -y
[root@ha-node01 ~]# iscsiadm --mode discoverydb --type sendtargets --portal ha-storage --discover
10.10.10.37:3260,1 iqn.2019-05.com.hacluster:target1

> iscsi server 에 로그인 합니다. 

[root@ha-node01 ~]# iscsiadm --mode node --targetname iqn.2019-05.com.hacluster:target1 --portal ha-storage --login

확인 

[root@ha-node01 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
fd0      2:0    1    4K  0 disk
sda      8:0    0   16G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0    1G  0 part [SWAP]
└─sda3   8:3    0   14G  0 part /
sdb      8:16   0    1G  0 disk
sdc      8:32   0    1G  0 disk
sdd      8:48   0    1G  0 disk
[root@ha-node01 ~]# 
```

- fdisk 작업  
> ha-node01 에서 작업  
> /dev/sdd /dev/sdc /dev/sdd  

```no-highlight
[root@ha-node01 ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x6a013082.

Command (m for help): p

Disk /dev/sdb: 1073 MB, 1073741824 bytes, 2097152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 33550336 bytes
Disk label type: dos
Disk identifier: 0x6a013082

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (65528-2097151, default 65528):
Using default value 65528
Last sector, +sectors or +size{K,M,G} (65528-2097151, default 2097151):
Using default value 2097151
Partition 1 of type Linux and of size 992 MiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): wq
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@ha-node01 ~]#
~ 생략 


- lvm 작업 
> lvm 생성은 ha-node01에서만 작업 합니다.  
> lvmconf 부분만 ha-node01 , ha-node02 에서 작업 합니다.   
[root@ha-node01 ~]# lvmconf --enable-cluster

[root@ha-node01 ~]# grep 'locking_type =' /etc/lvm/lvm.conf          <--- ha-node01 , ha-node02 에서 작업
    locking_type = 3

[root@ha-node01 ~]# pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1
[root@ha-node01 ~]# vgcreate -Ay -cy vg00 /dev/sdb1 /dev/sdc1 /dev/sdd1
[root@ha-node01 ~]# vi /etc/lvm/lvm.conf
locking_type = 0


[root@ha-node01 ~]# mkfs.xfs /dev/mapper/vg00-halvm -f
meta-data=/dev/mapper/vg00-halvm isize=512    agcount=4, agsize=184320 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=737280, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@ha-node01 ~]#
root@ha-node01 ~]# vi /etc/lvm/lvm.conf
locking_type = 3
```



- CLVM 작업  
> ha-node2 에서 작업  

```no-highlight
[root@ha-node01 ~]# pcs resource create dlm ocf:pacemaker:controld op monitor interval=30s on-fail=fence clone interleave=true ordered=true
[root@ha-node01 ~]# pcs resource create clvmd ocf:heartbeat:clvm op monitor interval=30s on-fail=fence clone interleave=true ordered=true
[root@ha-node01 ~]# pcs constraint order start dlm-clone then clvmd-clone
[root@ha-node01 ~]# pcs constraint colocation add clvmd-clone with dlm-clone
[root@ha-node01 ~]# pcs resource create halvm LVM volgrpname=vg00 exclusive=true --group test-svc
[root@ha-node01 ~]# mkdir /test
[root@ha-node02 ~]# mkdir /test                  <--- ha-node02 에서 작업 합니다.
[root@ha-node01 ~]# pcs resource create test-xfs Filesystem device=/dev/mapper/vg00-halvm directory=/test fstype=xfs force_unmount=true --group test-svc
```

> ha-node01 , ha-node02 에서 작업  

```no-highlight
[root@ha-node01 ~]# systemctl stop lvm2-lvmetad.socket
[root@ha-node01 ~]# systemctl stop lvm2-lvmetad.service
[root@ha-node01 ~]# systemctl disable lvm2-lvmetad.socket
[root@ha-node01 ~]# systemctl disable lvm2-lvmetad.service
[root@ha-node01 ~]# dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
[root@ha-node01 ~]# init 6 
```

> ha-node01 에서 resource 확인을 진행 합니다.  
> 노드 리부팅후 cleanup 후 정상적으로 cluster 구동이 안될경우 ha-node01 , ha-node02 에서  
> lvmconf --enable-cluster 실행후 pcs resource cleanup --all 을 합니다.  

```no-highlight
[root@ha-node01 ~]# pcs resource cleanup --all 
[root@ha-node01 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Tue May 21 23:15:55 2019
Last change: Tue May 21 23:15:50 2019 by hacluster via crmd on ha-node01

2 nodes configured
11 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Starting ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Starting ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01
     test-xfs   (ocf::heartbeat:Filesystem):    Started ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: dlm-clone [dlm]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: clvmd-clone [clvmd]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node01 ~]#

[root@ha-node01 ~]# df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/sda3                14G  1.3G   13G   9% /
devtmpfs                990M     0  990M   0% /dev
tmpfs                  1000M   73M  928M   8% /dev/shm
tmpfs                  1000M  8.9M  991M   1% /run
tmpfs                  1000M     0 1000M   0% /sys/fs/cgroup
/dev/sda1              1014M  137M  878M  14% /boot
tmpfs                   200M     0  200M   0% /run/user/0
/dev/mapper/vg00-halvm  2.9G   33M  2.8G   2% /test
[root@ha-node01 ~]#
```

> 서비스 이관 테스트  

```no-highlight
[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Tue May 21 23:18:41 2019
Last change: Tue May 21 23:18:26 2019 by hacluster via crmd on ha-node01

2 nodes configured
11 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Starting ha-node01
 fence_node02   (stonith:fence_vmware_soap):    Starting ha-node02
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node02
     halvm      (ocf::heartbeat:LVM):   Started ha-node02
     test-xfs   (ocf::heartbeat:Filesystem):    Started ha-node02
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: dlm-clone [dlm]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: clvmd-clone [clvmd]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
df  pcsd: active/enabled
 -h
[root@ha-node02 ~]# df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/sda3                14G  1.3G   13G   9% /
devtmpfs                990M     0  990M   0% /dev
tmpfs                  1000M   57M  944M   6% /dev/shm
tmpfs                  1000M  8.9M  991M   1% /run
tmpfs                  1000M     0 1000M   0% /sys/fs/cgroup
/dev/sda1              1014M  137M  878M  14% /boot
tmpfs                   200M     0  200M   0% /run/user/0
/dev/mapper/vg00-halvm  2.9G   33M  2.8G   2% /test
[root@ha-node02 ~]#
```


## httpd Resource 추가  
> ha-node01 , ha-node02 에서 작업  
> httpd 패키지를 설치 합니다.  
> httpd.conf 파일을 설정 합니다.  


```no-highlight
[root@ha-node01 ~]# yum install -y httpd wget
[root@ha-node01 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)
[root@ha-node01 ~]#



[root@ha-node01 ~]# vi /etc/httpd/conf/httpd.conf

#DocumentRoot "/var/www/html"
#<Directory "/var/www/html">
DocumentRoot "/test"
<Directory "/test">

IncludeOptional conf.d/*.conf
<Location /server-status>
  SetHandler server-status
  Order deny,allow
  Deny from all
  Allow from 127.0.0.1
</Location>
```

- httpd resource 추가  

```no-highlight
[root@ha-node01 ~]# pcs resource create web-service ocf:heartbeat:apache configfile="/etc/httpd/conf/httpd.conf" \
statusurl="http://127.0.0.1/server-status" --group test-svc

[root@ha-node02 ~]# pcs status
Cluster name: ha-scv

WARNINGS:
Following stonith devices have the 'action' option set, it is recommended to set 'pcmk_off_action', 'pcmk_reboot_action' instead: fence_node01, fence_node02

Stack: corosync
Current DC: ha-node01 (version 1.1.19-8.el7-c3c624ea3d) - partition with quorum
Last updated: Tue May 21 23:49:06 2019
Last change: Tue May 21 23:49:03 2019 by hacluster via crmd on ha-node02

2 nodes configured
12 resources configured

Online: [ ha-node01 ha-node02 ]

Full list of resources:

 fence_node01   (stonith:fence_vmware_soap):    Stopped
 fence_node02   (stonith:fence_vmware_soap):    Stopped
 Resource Group: test-svc
     VIP        (ocf::heartbeat:IPaddr2):       Started ha-node01
     halvm      (ocf::heartbeat:LVM):   Started ha-node01
     test-xfs   (ocf::heartbeat:Filesystem):    Started ha-node01
     web-service        (ocf::heartbeat:apache):        Starting ha-node01
 Clone Set: svcnet-monitor-clone [svcnet-monitor]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: dlm-clone [dlm]
     Started: [ ha-node01 ha-node02 ]
 Clone Set: clvmd-clone [clvmd]
     Started: [ ha-node01 ha-node02 ]

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
[root@ha-node02 ~]#
```

- httpd 디렉토리에 index.html 파일을 생성 합니다.  

```no-highlight
[root@ha-node02 ~]# vi /test/index.html
<html>
<body>ha-test</body>
</html>
```