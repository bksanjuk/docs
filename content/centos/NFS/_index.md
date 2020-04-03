+++
title = "NFS"
weight = 22
+++

## NFS  

{{% notice note %}}
**NFS 소개**  
NFS (Network File System)는 컴퓨터 사용자가 원격 컴퓨터의 파일을 사용자의 컴퓨터에있는  
것처럼보고 선택적으로 저장하고 업데이트 할 수있게 해주는 클라이언트/서버 응용 프로그램 입니다.  
NFS 프로토콜은 NAS (Network Attached Storage)를위한 여러 분산 파일 시스템 표준 중 하나입니다.  
{{% /notice %}}


* nfs 설치  
```no-highlight
[root@centos75 ~]# yum install -y nfs-utils
```

* nfs 실행 및 Daemon 활성화  
nfs 실행시 필요한 데몬들을 모두 구성 합니다.  
```no-highlight
[root@centos75 ~]# systemctl start nfs-server
```

* nfs 디렉토리 생성  
```no-highlight
[root@centos75 ~]# mkdir /nfs-data
[root@centos75 ~]# chown nfsnobody:nfsnobody /nfs-data
```

- nfs 설정  
> /etc/exports 에 nfs 사용할 공유 디렉토리를 설정 합니다.  
> 모든 * ip 대역에서 접슥을 가능 하게 하며 읽기 전용 으로 파일시스템이 변경 되면  
> 즉시 됭기화 하게 설정 합니다.  

```no-highlight
[root@centos75 ~]# vi /etc/exports
/nfs-data  *(ro,sync)
[root@centos75 ~]# systemctl reload nfs-server
[root@centos75 ~]# exportfs
/nfs-data       <world>
[root@centos75 ~]#
```

* nfs /etc/exports 옵션  

|옵션    	|설명    	|
|---	|---	|
|ro   	|읽기 전용으로만 연결을 허용 합니다.    	|
|rw   	|읽기 쓰기 가능하게 연결을 허용 합니다.    	|
|root_squash   	|NFS 설정시 기본값이 되며 클라이언트 측에 관리자가 접속요청시 익명계정으로 연결을 허용 합니다.    	|
|no_root_squash   	|클라이언트 측에 관리자가 접속 요청을 했을때 서버 측에서의 관리자 계정으로 맵핑 시켜 연결 합니다.    	|
|all_squash   	|클라이언트 측에서 사용자로 접근 요청 했을 때 익명 계정으로 연결을 허용 합니다.    	|
|anonuid=[UID]   	|클라이언트 측에서 익명으로 접근 요청시 지정한 UID 값의 계정으로 연결 합니다.    	|
|anongid=[GID]   	|클라이언트 측에서 익명으로 접근 요청시 지정한 GID 값의 그룹 으로 연결 합니다.    	|
|sync   	|파일 시스템이 변경 되며 즉시 동기화 합니다.    	|
|noaccess   	|지정된 디렉토리에는 접근을 거부 합니다.    	|
|secure   	|클라이언트 마운트 요청시 포트를 1024 이하로 합니다.    	|
|insecure   	|인증되지 않은 접근도 접근 가능 합니다.     	|

* 더많은 옵션은 man exports 에서 확인 할수 있습니다.  


* 방화벽 설정  
```no-highlight
[root@centos75 ~]# firewall-cmd --permanent --add-service=nfs
[root@centos75 ~]# firewall-cmd --permanent --add-service=rpc-bind
[root@centos75 ~]# firewall-cmd --reload
```


* nfs client 설정  

> nfs 사용을 위하여 패키지를 설치 합니다.  
```no-highlight
[root@centos7 ~]# yum install -y nfs-utils
```

* showmount -e $nfs-server
nfs-server 의 공유 디렉토리를 확인 합니다.  
```no-highlight
[root@centos7 ~]# showmount -e 192.168.0.11
Export list for 192.168.0.11:
/nfs-data *
[root@centos7 ~]#
```

* mount 명령어를 이용한 nfs-server mount  

```no-highlight
[root@centos7 ~]# df -h
Filesystem              Size  Used Avail Use% Mounted on
/dev/sda3                16G  4.5G   12G  29% /
devtmpfs                1.9G     0  1.9G   0% /dev
tmpfs                   1.9G     0  1.9G   0% /dev/shm
tmpfs                   1.9G   13M  1.9G   1% /run
tmpfs                   1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1               2.0G  178M  1.9G   9% /boot
tmpfs                   378M   28K  378M   1% /run/user/0
tmpfs                   378M   12K  378M   1% /run/user/42
192.168.0.11:/nfs-data   16G  4.5G   12G  29% /mnt
[root@centos7 ~]#
```

* /etc/fstab 에 등록  

```no-highlight
[root@centos7 ~]# vi /etc/hosts
[root@centos7 ~]# vi /etc/hosts
192.168.0.11 nfs
[root@centos7 ~]# vi /etc/fstab
nfs:/nfs-data                             /data           nfs     ro,hard,bg,intr,tcp,rsize=16384,wsize=16384 0 0
[root@centos7 ~]# mkdir /data
[root@centos7 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        16G  2.0G   14G  13% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G   12M  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1       2.0G  168M  1.9G   9% /boot
tmpfs           378M     0  378M   0% /run/user/0
nfs:/nfs-data    16G  4.5G   12G  29% /data
[root@centos7 ~]#
```

* nfs /etc/fstab 설정 옵션

|옵션    	|설명    	|
|---	|---	|
|ro   	|읽기 전용으로 마운트 합니다.    	|
|rw   	|읽기 쓰기 가능으로 마운트 합니다.    	|
|rsize   	|지정된 nfs 서버로 부터 읽어 오는 바이트수를 지정 , 기본값 1024   	|
|wsize   	|지정된 nfs 서버로 부터 쓰기를 할때 사용하는 바이트 수를 지정 , 기본값 1024   	|
|timeo   	|rpc 타임아웃이 발생 되고 나서 첫번째 재전송 요구를 보낼때 사용되는 시간    	|
|retrans   	|timeout 발생시 재전송 시도 횟수를 제한한 것, 기본값 3 입니다.    	|
|port   	| 지정된 nfs 연결시 port 번호를 지정    	|
|fg   	|첫번째 마운트 시도하여 timeout 되면 바로 중단됩니다. default 값    	|
|intr   	|timeout 발생시 신호를 보내는 nfs 호출을 인터럽트 합니다.    	|
|hard   	|timeout 이 발생하면 "server not responding" 메시지를 출력한후 계속 재시도합니다.    	|
|soft   	|timeout 이 발생하면 i/o 에러가 발생하였음을 알려줍니다.    	|
|noac   	|모든 속성의 캐쉬를 해제 합니다. 서버 효율이 떨어질수 있으나 다른 nfs 클라이언트 사용시 공통 화일 시스템에 쓰기 작업시 좋은 효율을 얻을수 있습니다.    	|
|tcp   	|nfs 파일 시스템을 기본값인 udp 가 아닌 tcp 프로토콜을 사용하여 마운트 합니다.    	|
|retry   	|백그라운드 에서 진행중인 nfs 마운트 작업이 포기하기 전까지 실행할 횟수를 지정 합니다. , 기본값 10000번 입니다.     	|


{{% notice tip %}}
nfsstat 명령어  
NFS 클라이언트 및 서버 작업에 대한 통계를 표시합니다.  
{{% /notice %}}



```no-highlight
[root@centos7 ~]# nfsstat
Client rpc stats:
calls      retrans    authrefrsh
125        0          125

Client nfs v4:
null         read         write        commit       open         open_conf
0         0% 0         0% 0         0% 0         0% 0         0% 0         0%
open_noat    open_dgrd    close        setattr      fsinfo       renew
0         0% 0         0% 0         0% 0         0% 13       11% 0         0%
setclntid    confirm      lock         lockt        locku        access
0         0% 0         0% 0         0% 0         0% 0         0% 5         4%
getattr      lookup       lookup_root  remove       rename       link
10        8% 8         6% 5         4% 0         0% 0         0% 0         0%
symlink      create       pathconf     statfs       readlink     readdir
0         0% 0         0% 8         6% 3         2% 0         0% 0         0%
server_caps  delegreturn  getacl       setacl       fs_locations rel_lkowner
21       17% 0         0% 0         0% 0         0% 0         0% 0         0%
secinfo      exchange_id  create_ses   destroy_ses  sequence     get_lease_t
0         0% 0         0% 4         3% 4         3% 3         2% 25       21%
reclaim_comp layoutget    getdevinfo   layoutcommit layoutreturn getdevlist
0         0% 4         3% 0         0% 0         0% 0         0% 0         0%
(null)
5         4%

[root@centos7 ~]#
```

* 모든 버전의 NFS에 대한 모든 정보 표시  
```no-highlight
[root@centos7 ~]# nfsstat -o all -234
```

* NFS의 활성 버전에 대한 모든 정보 표시  
```no-highlight
[root@centos7 ~]# nfsstat -o all
```

* 마운트 된 NFS 파일 시스템에 대한 정보 표시  
```no-highlight
[root@centos7 ~]# nfsstat -m
```

<br></br>