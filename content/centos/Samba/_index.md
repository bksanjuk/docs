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
[root@samba ~]# yum install -y samba-*
```

* Samba Daemon 실행 및 활성화  
```no-highlight
[root@samba ~]# systemctl start smb;systemctl start nmb
[root@samba ~]# systemctl enable smb;systemctl enable nmb
```


{{% notice info %}}
Samba 설정 파일  
lmhosts 파일의 경우 삼바서버 NetBIOS 호스트 파일 입니다.  
삼바서버에서 사용하는 호스트 정보파일 입니다.  
smb.conf 와 smb.conf.example 파일이 있습니다.  
example 파일은 참고용 파일이며 설정값이 들어가는 파일은 smb.conf 파일 입니다.  
{{% /notice %}}




```no-highlight
[root@samba ~]# ls -al /etc/samba/
total 32
drwxr-xr-x   2 root root    61 Apr  4 01:27 .
drwxr-xr-x. 80 root root  8192 Apr  4 01:27 ..
-rw-r--r--   1 root root    20 Dec  3 02:48 lmhosts
-rw-r--r--   1 root root   706 Dec  3 02:48 smb.conf
-rw-r--r--   1 root root 11327 Dec  3 02:48 smb.conf.example
[root@samba ~]#

[root@samba ~]# cat /etc/samba/smb.conf
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = WORK  <--default SAMBA 에서 WORK 로 변경  
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
[root@samba ~]#

```


* smb.conf 수정  

```no-highlight
[root@samba ~]# vi /etc/samba/smb.conf
[share]
        comment = share data
        path = /share
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
        workgroup = WORK
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
[root@samba ~]# testparm
rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
Registered MSG_REQ_POOL_USAGE
Registered MSG_REQ_DMALLOC_MARK and LOG_CHANGED
Load smb config files from /etc/samba/smb.conf
rlimit_max: increasing rlimit_max (1024) to minimum Windows limit (16384)
Processing section "[homes]"
Processing section "[printers]"
Processing section "[print$]"
Processing section "[share]"
Loaded services file OK.
Server role: ROLE_STANDALONE

Press enter to see a dump of your service definitions

# Global parameters
[global]
        printcap name = cups
        security = USER
        workgroup = WORK
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
        path = /share
        read only = No
[root@samba ~]#

```


* Samba Test 디렉토리 생성 및 User 생성  
```no-highlight
# SAMBA 디렉토리 생성 
[root@samba ~]# mkdir /share
[root@samba ~]# chmod 777 /share
[root@samba ~]# smbpasswd -a test
New SMB password:
Retype new SMB password:
Added user test.
[root@samba ~]#


# User 생성 
[root@samba ~]# useradd test
[root@samba ~]# smbpasswd -a test
New SMB password:
Retype new SMB password:
Added user test.
[root@samba ~]#


# 방화벽 오픈 
[root@samba ~]# firewall-cmd --permanent --add-service=samba
[root@samba ~]# firewall-cmd --reload

```

{{% notice tip %}}
Samba mount 테스트  
윈도우즈 이용시 윈도우 key + R 에서 \\host-ip 로 접속 userID/Pass 입력후 접속 하시면 됩니다.  
Linux 이용시 아래와 같이 mount -t cifs -o username=$User_name,password=$Password //호스트/공유이름 /마운트포인트 로 Mount 할수 있습니다.  
{{% /notice %}}

```no-highlight
# SAMBA Client 에서 cifs util을 설치 합니다.  
[root@client ~]# yum install -y samba-client cifs-utils


# SAMBA 접속 테스트 
[root@client ~]# smbclient -L //192.168.0.10/share -U test
Enter SAMBA\test's password:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share           Disk      share data
        IPC$            IPC       IPC Service (Samba 4.9.1)
        test            Disk      Home Directories
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------
        SAMBA                Samba 4.9.1

        Workgroup            Master
        ---------            -------
        SAMBA                SAMBA
[root@client ~]#


# SAMBA mount  
[root@client ~]# mount -t cifs -o username=test,password=password //192.168.0.10/share /mnt
[root@client ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
devtmpfs              1.9G     0  1.9G   0% /dev
tmpfs                 1.9G     0  1.9G   0% /dev/shm
tmpfs                 1.9G   12M  1.9G   1% /run
tmpfs                 1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda3             8.0G  1.3G  6.8G  16% /
/dev/sda1            1014M  141M  874M  14% /boot
tmpfs                 378M     0  378M   0% /run/user/0
//192.168.0.10/share  8.0G  2.5G  5.6G  31% /mnt
[root@client ~]#

[root@client ~]# cd /mnt
[root@client mnt]# ls
[root@client mnt]# touch 1
[root@client mnt]# touch 2
[root@client mnt]# ls -al
total 0
drwxr-xr-x   2 root root   0 Apr  4 02:16 .
dr-xr-xr-x. 17 root root 244 Apr  4 01:25 ..
-rwxr-xr-x   1 root root   0 Apr  4 02:16 1
-rwxr-xr-x   1 root root   0 Apr  4 02:16 2
[root@client mnt]#


#fstab 등록시 password 파일 생성 후 /etc/fstab 을 수정 해야 합니다.  
[root@client ~]# vi /root/pass.txt
username=test
password=password


#fstab 수정 
[root@client ~]# vi /etc/fstab
~중략
//192.168.0.10/share                    /mnt                      cifs    cred=/root/pass.txt 0 0


# mount 
[root@client ~]# mount -a
[root@client ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
devtmpfs              1.9G     0  1.9G   0% /dev
tmpfs                 1.9G     0  1.9G   0% /dev/shm
tmpfs                 1.9G   12M  1.9G   1% /run
tmpfs                 1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda3             8.0G  1.3G  6.8G  16% /
/dev/sda1            1014M  141M  874M  14% /boot
tmpfs                 378M     0  378M   0% /run/user/0
//192.168.0.10/share  8.0G  2.5G  5.6G  31% /mnt
[root@client ~]#

```

{{% notice info %}}
smbclient Samba Server 확인  
smbclient 명령어를 이용하여 samba Server 의 정보를 확인 할수 있습니다.  
{{% /notice %}}

```no-highlight
[root@client ~]# smbclient -L 192.168.0.10 -U%
\
        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share           Disk      share data                         <-- share
        IPC$            IPC       IPC Service (Samba 4.9.1)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------
        SAMBA                Samba 4.9.1

        Workgroup            Master
        ---------            -------
        SAMBA                SAMBA
[root@client ~]# \

```

{{% notice note %}}
smbclient Samba Server 접속  
마운트 없이 단순하게 접속하여 확인시 사용합니다.  
-U 옵션을 사용하여 유저를 지정 할수 있습니다.  
{{% /notice %}}

```no-highlight
[root@client ~]# smbclient -U test //192.168.0.10/share
Enter SAMBA\test's password:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 01:40:52 2020
  ..                                  D        0  Sat Apr  4 01:40:52 2020

                8377344 blocks of size 1024. 5785376 blocks available
smb: \>
```


> Samba 접속자 확인  
접속자 확인시 smbstatus 를 사용 합니다.  

```no-highlight
[root@samba ~]# smbstatus

Samba version 4.9.1
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing
----------------------------------------------------------------------------------------------------------------------------------------
2393    test         test         192.168.0.20 (ipv4:192.168.0.20:58354)    SMB3_02           -                    partial(AES-128-CMAC)

Service      pid     Machine       Connected at                     Encryption   Signing
---------------------------------------------------------------------------------------------
IPC$         2393    192.168.0.20  Sat Apr  4 01:52:50 AM 2020 KST  -            -
share        2393    192.168.0.20  Sat Apr  4 01:52:50 AM 2020 KST  -            -

No locked files

[root@samba ~]#

```

> Public share 디렉토리  
> 별도의 유저 없이 사용할수 있는 디렉토리 설정의 경우 아래 내용으로 사용이 가능 합니다.  
> [설정참고 wiki.samba.org](https://wiki.samba.org/index.php/Setting_up_Samba_as_a_Standalone_Server)  

```no-highlight
[root@samba ~]# vi /etc/samba/smb.conf

[global]
        workgroup = WORK
        security = user

        passdb backend = tdbsam
        map to guest = Bad User    <-- map to guest 옵션을 추가 합니다.  

        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw



[pub]
        comment = public data
        path = /public
        printable = no
        public = yes
        writable = yes
        create mask = 0777
        directory mask = 0777

[root@samba ~]# mkdir /public
[root@samba ~]# chmod 777 /public/
[root@samba ~]# systemctl restart smb ; systemctl restart nmb
```


* Public share 디렉토리 확인  
```no-highlight
[root@client ~]# smbclient -L 192.168.0.10 -U%

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        share           Disk      share data                           <-- share
        pub             Disk      public data                          <-- pub
        IPC$            IPC       IPC Service (Samba 4.9.1)
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORK                 SAMBA
[root@client ~]#

```

* Public share 디렉토리 mount  
패스워드 입력시 엔터를 치면 됩니다.  
```no-highlight
[root@client ~]# mount -t cifs  //192.168.0.10/pub /mnt
Password for root@//192.168.0.10/pub:
[root@client ~]# df -h
Filesystem          Size  Used Avail Use% Mounted on
devtmpfs            1.9G     0  1.9G   0% /dev
tmpfs               1.9G     0  1.9G   0% /dev/shm
tmpfs               1.9G   12M  1.9G   1% /run
tmpfs               1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda3           8.0G  1.3G  6.8G  16% /
/dev/sda1          1014M  141M  874M  14% /boot
tmpfs               378M     0  378M   0% /run/user/0
//192.168.0.10/pub  8.0G  2.5G  5.6G  31% /mnt
[root@client ~]#
```

{{% notice tip %}}
System User Samba 이용  
기본적으로 System User 의 경우 /home/$User 디렉토리를 사용할수 있습니다.  
smbpasswd -a $USER 로 추가  
smbpasswd -x $USER 삭제 할수 있습니다.  
{{% /notice %}}
   


<br></br>