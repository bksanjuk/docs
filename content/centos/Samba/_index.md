+++
title = "Samba"
weight = 19
+++


## Samba

{{% notice note %}}
network 를 통한 이기종 linux / window 파일 디렉토리 공유  
프린터 공유시 Samba 를 이용합니다.  
[Samba Officail Site](https://www.samba.org/)에서 자세한 정보를 확인 할수 있습니다.  
프린트 공유시에는 Samba Server 에 cups Daemon 이 동작해야 합니다.  
{{% /notice %}}


* Samba 설치  

```no-highlight
[root@centos7 ~]# yum install -y samba-*
```

* Samba Daemon 실행 및 활성화  
```no-highlight
[root@centos7 ~]# systemctl start smb
[root@centos7 ~]# systemctl enable smb
```


{{% notice info %}}
Samba 설정 파일  
lmhosts 파일의 경우 삼바서버 NetBIOS 호스트 파일 입니다.  
삼바서버에서 사용하는 호스트 정보파일 입니다.  
smb.conf 와 smb.conf.example 파일이 있습니다.  
example 파일은 참고용 파일이며 설정값이 들어가는 파일은 smb.conf 파일 입니다.  
{{% /notice %}}




```no-highlight
[root@centos7 ~]# ls -al /etc/samba/
합계 32
drwxr-xr-x   2 root root    61 10월 16 22:07 .
drwxr-xr-x. 81 root root  8192 10월 16 21:59 ..
-rw-r--r--   1 root root    20  8월 17 00:49 lmhosts
-rw-r--r--   1 root root   706  8월 17 00:49 smb.conf
-rw-r--r--   1 root root 11327  8월 17 00:49 smb.conf.example
[root@centos7 ~]#
[root@centos7 ~]# cat /etc/samba/smb.conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = SAMBA
        security = user

        passdb backend = tdbsam

        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes

[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
[root@centos7 ~]#

```


* smb.conf 수정  

```no-highlight
[root@centos7 ~]# vi /etc/samba/smb.conf
[share]
        comment = share data
        path = /smb-test
        printable = no
        public = no
        writable = yes
        create mask = 0777
        directory mask = 0777


```

* share 필드 설명  

|설정값    	|설명    	|
|---	|---	|
|comment   	|공유 디렉토리 설명    	|
|path   	|공유 디렉토리 위치    	|
|printable   	|printer 사용 여부    	|
|public   	|익명접근 가능여부    	|
|writable   	|쓰기 가능여부    	|
|create mask   	|파일 권한    	|
|directory mask   	|디렉토리 권한    	|

* global 필드 설명  


|설정값    	|설명    	|
|---	|---	|
|workgroup   	|작업구룹명 삼바 서버와 클라이언트의 작업구릅은 반드시 일치해야 합니다.      	|
|security   	|share 값은 더이상 사용되지 않습니다. user Default 사용권장     	|
|passdb backend   	|패스워드에 대한 인증방식     	|
|map to guest   	|계정이 없는 사용자를 Guest 로 지정 합니다.     	|
|printing   	|printing server 로 사용시 설정 Default 사용권장        	|
|printcap name   	|printing server 로 사용시 설정 Default 사용권장      	|
|load printers   	|printing server 사용시 설정      	|
|server string   	|윈도우 네트워크 환경에서 보여줄 삼바 서버에 대한 설명     	|
|interfaces   	|Samba Server 인터페이스를 지정 할수 있습니다.     	|
|hosts allow   	|접근 호스트 allow 설정     	|
|unix charset   	|문자셋 을 지정 할수 있습니다. utf-8 설정을 지정 할수 있습니다.     	|
|cpus options   	|cpus 옵션을 지정    	|


* 설정 내용 참고  
아래 내용은 단순 참고 자료 입니다.  

```no-highlight
[global]
        workgroup = SAMBA
        security = user
        passdb backend = tdbsam
        map to guest = Bad User
        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw
        server string = Samba Server Version %v
        interfaces = lo ens33 192.168.0.10/24
        hosts allow = 192.168.0
        unix charset=utf-8
```





* smb.conf 오류 체크  

```no-highlight
[root@centos7 ~]# testparm
Load smb config files from /etc/samba/smb.conf
rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
Processing section "[homes]"
Processing section "[printers]"
Processing section "[print$]"
Processing section "[share]"
Processing section "[pub]"
Loaded services file OK.
Server role: ROLE_STANDALONE

Press enter to see a dump of your service definitions

# Global parameters
[global]
        map to guest = Bad User
        printcap name = cups
        security = USER
        workgroup = SAMBA
        idmap config * : backend = tdb
        cups options = raw


[homes]
        browseable = No
        comment = Home Directories
        inherit acls = Yes
        read only = No
        valid users = %S %D%w%S


[printers]
        browseable = No
        comment = All Printers
        create mask = 0600
        path = /var/tmp
        printable = Yes


[print$]
        comment = Printer Drivers
        create mask = 0664
        directory mask = 0775
        force group = @printadmin
        path = /var/lib/samba/drivers
        write list = @printadmin root


[share]
        comment = share data
        create mask = 0777
        directory mask = 0777
        path = /smb-test
        read only = No


[pub]
        comment = public data
        create mask = 0777
        directory mask = 0777
        guest ok = Yes
        path = /public
        read only = No
[root@centos7 ~]#
```


* Samba Test 디렉토리 생성 및 User 생성  
```no-highlight
[root@centos7 ~]# mkdir /smb-test
[root@centos7 ~]# chmod 777 /smb-test
[root@centos7 ~]# smbpasswd -a test
New SMB password:
Retype new SMB password:
Added user test.
[root@centos7 ~]#
```

{{% notice tip %}}
Samba mount 테스트  
윈도우즈 이용시 윈도우 key + R 에서 \\host-ip 로 접속 userID/Pass 입력후 접속 하시면 됩니다.  
Linux 이용시 아래와 같이 mount -t cifs -o username=x,password=xx //호스트/공유이름 /마운트포인트 로 Mount 하시면 됩니다.  
{{% /notice %}}

```no-highlight
[root@centos75 ~]# mount -t cifs -o username=test,password=test-password //192.168.0.10/share /mnt

[root@centos75 ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sda3              16G  4.6G   12G  29% /
devtmpfs              1.9G     0  1.9G   0% /dev
tmpfs                 1.9G     0  1.9G   0% /dev/shm
tmpfs                 1.9G   13M  1.9G   1% /run
tmpfs                 1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1             2.0G  178M  1.9G   9% /boot
tmpfs                 378M   28K  378M   1% /run/user/0
tmpfs                 378M   12K  378M   1% /run/user/42
//192.168.0.10/share   16G  2.0G   15G  13% /mnt
[root@centos75 ~]#
```

{{% notice info %}}
smbclient Samba Server 확인  
smbclient 명령어를 이용하여 samba Server 의 정보를 확인 할수 있습니다.  
{{% /notice %}}

```no-highlight
[root@centos75 ~]# smbclient -L 192.168.0.10 -U%

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share           Disk      share data
        IPC$            IPC       IPC Service (Samba 4.7.1)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
[root@centos75 ~]#
```

{{% notice note %}}
smbclient Samba Server 접속  
마운트 없이 단순하게 접속하여 확인시 사용합니다.  
-U 옵션을 사용하여 유저를 지정 할수 있습니다.  
{{% /notice %}}

```no-highlight
[root@centos75 ~]# smbclient -U test //192.168.0.10/share
Enter SAMBA\test's password:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Oct 16 22:32:17 2018
  ..                                 DR        0  Tue Oct 16 22:22:35 2018

                16765952 blocks of size 1024. 14682328 blocks available
smb: \> pwd
Current directory is \\192.168.0.10\share\
smb: \> quit
[root@centos75 ~]#
```


> Samba 접속자 확인  
접속자 확인시 smbstatus 를 사용 합니다.  

```no-highlight
[root@centos7 ~]# smbstatus

Samba version 4.7.1
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing
----------------------------------------------------------------------------------------------------------------------------------------
4007    test         test         192.168.0.11 (ipv4:192.168.0.11:51252)    NT1               -                    -

Service      pid     Machine       Connected at                     Encryption   Signing
---------------------------------------------------------------------------------------------
share        4007    192.168.0.11  Tue Oct 16 10:42:30 PM 2018 KST  -            -
IPC$         4007    192.168.0.11  Tue Oct 16 10:42:30 PM 2018 KST  -            -

No locked files

[root@centos7 ~]#
```

> Public share 디렉토리  
> 별도의 유저 없이 사용할수 있는 디렉토리 설정의 경우 아래 내용으로 사용이 가능 합니다.  
> [설정참고 wiki.samba.org](https://wiki.samba.org/index.php/Setting_up_Samba_as_a_Standalone_Server)  

```no-highlight

[root@centos7 ~]# vi /etc/samba/smb.conf
[global]
        workgroup = SAMBA
        security = user

        passdb backend = tdbsam
        map to guest = Bad User


[pub]
        comment = public data
        path = /public
        printable = no
        public = yes
        writable = yes
        create mask = 0777
        directory mask = 0777


[root@centos7 ~]# mkdir /public
[root@centos7 ~]# chmod 777 /public/
[root@centos7 ~]# systemctl reload smb 
```


* Public share 디렉토리 확인  
```no-highlight
[root@centos75 ~]# smbclient -L 192.168.0.10 -U%

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share           Disk      share data
        IPC$            IPC       IPC Service (Samba 4.7.1)
        pub             Disk      public data
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
[root@centos75 ~]#
```

* Public share 디렉토리 mount  
패스워드 입력시 엔터를 치면 됩니다.  
```no-highlight
[root@centos75 ~]# mount -t cifs  //192.168.0.10/pub /mnt
Password for root@//192.168.0.10/pub:
[root@centos75 ~]# df -h
Filesystem          Size  Used Avail Use% Mounted on
/dev/sda3            16G  4.5G   12G  29% /
devtmpfs            1.9G     0  1.9G   0% /dev
tmpfs               1.9G     0  1.9G   0% /dev/shm
tmpfs               1.9G   13M  1.9G   1% /run
tmpfs               1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1           2.0G  178M  1.9G   9% /boot
tmpfs               378M   28K  378M   1% /run/user/0
tmpfs               378M   12K  378M   1% /run/user/42
//192.168.0.10/pub   16G  2.0G   15G  13% /mnt
[root@centos75 ~]#
```

* Samba 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-service=samba
[root@centos7 ~]# firewall-cmd --reload
```

{{% notice tip %}}
System User Samba 이용  
기본적으로 System User 의 경우 /home/$User 디렉토리를 사용할수 있습니다.  
smbpasswd -a $USER 로 추가  
smbpasswd -x $USER 삭제 할수 있습니다.  
{{% /notice %}}
   


<br></br>