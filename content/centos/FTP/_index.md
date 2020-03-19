+++
title = "FTP"
weight = 17
+++


## FTP

{{% notice note %}}
CentOS 에서는 FTP Server 로 vsftpd 를 이용합니다.  
[vsftpd](https://security.appspot.com/vsftpd.html) 에서 자세한 정보를 확인할수 있습니다.  
{{% /notice %}}


* vsftpd 설치  
```no-highlight
[root@centos7 ~]# yum install vsftpd
```

* vsftpd daemon 실행 및 활성화  
```no-highlight
[root@centos7 ~]# systemctl enable vsftpd
[root@centos7 ~]# systemctl start vsftpd
```

* user 접속 설정 파일  
> vsftpd 에서는 기본으로 root 유저의 접속이 Denied 되어 있습니다.  
> root 접속이 필요 한 경우 user_list 와 ftpusers 파일의 root 라인에 # 주석처리가 필요 합니다.  

```no-highlight
[root@centos7 ~]# cat /etc/vsftpd/user_list
# vsftpd userlist
# If userlist_deny=NO, only allow users in this file
# If userlist_deny=YES (default), never allow users in this file, and
# do not even prompt for a password.
# Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers
# for users that are denied.
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody
[root@centos7 ~]# cat /etc/vsftpd/ftpusers
# Users that are not allowed to login via ftp
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
news
uucp
operator
games
nobody
[root@centos7 ~]#
```

* vsftpd.conf Default 설정값  

```no-highlight
[root@centos7 ~]# cat /etc/vsftpd/vsftpd.conf

anonymous_enable=YES 
local_enable=YES     
write_enable=YES
local_umask=022
#anon_upload_enable=YES
#anon_mkdir_write_enable=YES
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
#chown_uploads=YES
#chown_username=whoever
#xferlog_file=/var/log/xferlog
xferlog_std_format=YES
#idle_session_timeout=600
#data_connection_timeout=120
#nopriv_user=ftpsecure
#async_abor_enable=YES
#ascii_upload_enable=YES
#ascii_download_enable=YES
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service.
#deny_email_enable=YES
#banned_email_file=/etc/vsftpd/banned_emails
#chroot_local_user=YES
#chroot_list_enable=YES
# (default follows)
#chroot_list_file=/etc/vsftpd/chroot_list
#ls_recurse_enable=YES
listen=NO
listen_ipv6=YES
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
```

* vsftpd.conf 옵션 정리  
> /etc/vsftpd/vsftpd.conf 내용으로 착성 되었습니다.  

|설정값    	|설명    	|
|---	|---	|
|anonymous_enable   	|anonymous 사용자 접속 허용여부   	|
|local_enable   	|local 사용자 허용여부    	|
|write_enable   	|write 명령어 허용여부    	|
|local_umask   	|local 사용자용 umask값     	|
|anon_upload_enable   	|anonymous 사용자 upload 허용여부    	|
|anon_mkdir_write_enable   	|anonymous 사용자 디렉토리 생서 허용여부    	|
|dirmessage_enable   	|접속시 출력 메세지 사용자 홈디렉토리에 .message 파일에 작성    	|
|xferlog_enable    	|xferlog 표준 포맷으로 로그를 남길지 여부   	|
|connect_from_port_20   	|20번 포트의 데이터전송 연결을 허용여부     	|
|chown_uploads    	|anonymous 사용자가 업로드한 파일의 소유권을 자동으로 변경하도록 설정   	|
|chown_username   	|anonymous 사용자가 업로드한 파일의 소유권을 변경할 유저명을 설정   	|
|xferlog_file   	|파일 전송 로그를 기록할 파일명   	|
|xferlog_std_format    	| xferlog 형식으로 ftp log를 남기기 위한 설정 detail 로그 설정시 no 로 설정합니다.    	|
|idle_session_timeout   	|i클라이언트 세션을 끊을 때 까지의 대기시간   	|
|data_connection_timeout   	|data connection을 끊을때 까지의 대기시간   	|
|nopriv_user   	|vsftpd 구동될 서버의 유저를 설정   	|
|async_abor_enable   	|async ABOR 명령어를 사용여부   	|
|ascii_upload_enable   	|ASCII 모드로 업로드 허용여부   	|
|ascii_download_enable    	|ASCII 모드로 다운로드 허용여부   	|
|ftpd_banner   	|ftp 접속 메세지 설정   	|
|deny_email_enable   	|로그인 거부 리스트 사용에 대한 설정   	|
|banned_email_file   	|로그인 거부 리스트 사용에 대한 설정   	|
|chroot_local_user    	|local user chroot 활성여부    	|
|chroot_list_enable    	|특정 사용자 list를 지정하여 chroot 적용여부    	|
|chroot_list_file   	|chroot_list_enable 과 같이 사용됩니다.    	|
|ls_recurse_enable   	|디렉토리 내용을 출력할때 cache 사용여부   	|
|listen   	|standalone으로 운영시 설정   	|
|listen_ipv6    	|ipv6 로 vsftpd 운영시 사용   	|
|pam_service_name   	|vsftpd 에서 사용할 pam 서비스 이름   	|
|userlist_enable   	|user_list 사용여부    	|
|tcp_wrappers    	|tcp_wrappers 사용여부 hosts.allow / hosts.deny 사용여부    	|


* 방화벽 추가  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --add-port=21/tcp
[root@centos7 ~]# firewall-cmd --reload
```


* anonymous user 설정및 특정 디렉토리로 접속  

{{% notice info %}}
anonymous ftp 사용  
anonymous ftp 경우 보안상 위험성이 있기 때문에 사용을 권장 하지 않습니다.  
system local user 가 아닌 ftp 사용의 경우 Virtual User 를 사용하는것이 좋습니다.  
{{% /notice %}}

> vsftpd.conf 에서 anonymous_enable=yes 가 되어 있을 경우  
> default 접속 디렉토리는 /var/ftp/pub 입니다.  
<br></br>

- anonymous 디렉토리 생성 및 권한 설정  
> /anon-data 디렉토리에 바로 권한을 설정 할수 없음으로 익명 업로드용 디렉토리를 생성 하고  
>권한을 777 로 줍니다.  

```no-highlight
[root@centos7 ~]# mkdir -p /anon-data/anonymous
[root@centos7 ~]# chmod a-w /anon-data/
[root@centos7 ~]# chmod 777 /anon-data/anonymous/
```

* vsftpd.conf 설정 변경및 daemon 재시작  
```no-highlight
[root@centos7 ~]# vi /etc/vsftpd/vsftpd.conf
anon_root=/anon-data
anon_upload_enable=YES
[root@centos7 ~]# systemctl restart vsftpd
```

* 접속 테스트  
> anonymous 사용자 업로드용 디렉토리는 /anon-data/anonymous 입니다.  
```no-highlight
[root@centos7 ~]# ftp localhost
Trying ::1...
Connected to localhost (::1).
220 (vsFTPd 3.0.2)
Name (localhost:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||32151|).
150 Here comes the directory listing.
drwxrwxrwx    2 0        0               6 Oct 15 08:21 anonymous
226 Directory send OK.
ftp>
```

{{% notice note %}}
active mode 와  passive mode  
active mode 의 경우 ftp 데이터 통신시 20번 포트를 이용한 방법 입니다.  
passive mode 는 1024 port 이후 ftp 데이터 포트를 미리 지정하여 해당 포트로 Client 가 접속하여 이용하는 방식입니다.  
{{% /notice %}}


* vsftp passive mode 사용  
> vsftpd.conf 를 수정 합니다.  
```no-highlight
[root@centos7 ~]# vi /etc/vsftpd/vsftpd.conf
#passive mod Settings
pasv_enable=YES
pasv_min_port=40001
pasv_max_port=40020
[root@centos7 ~]# systemctl restart vsftpd
```

* 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-port=40001-40020/tcp
[root@centos7 ~]# firewall-cmd --reload
```

* ftp 접속 테스트  
> 별도의 Client 를 이용하여 접속 합니다.  
```no-highlight
[root@CentOS7-client ~]# ftp 192.168.0.10
Connected to 192.168.0.10 (192.168.0.10).
220 (vsFTPd 3.0.2)
Name (192.168.0.10:root): test
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
227 Entering Passive Mode (192,168,0,10,156,75).
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
257 "/home/test"
ftp> 221 Goodbye.
[root@CentOS7-client ~]#
```

* vsftp port 접속 확인  
> test 시 Client 에서 ftp 2번 로그 하여 40001 과 40009 번 포트를 사용하는것을 확인 할수 있습니다.  
```no-highlight
[root@centos7 ~]# netstat -antp |grep -i 4000
tcp6       0      0 192.168.0.10:40009      192.168.0.4:41048       TIME_WAIT   -
tcp6       0      0 192.168.0.10:40001      192.168.0.4:41015       TIME_WAIT   -
[root@centos7 ~]#
```



* **Vsftpd Virtual Users Setting 1**

> Virtual Users 공통 사항  
> yum install db4-utils db4 설치가 필요 합니다.  

> vsftpd 를 이용한 virtual user 구성 방법입니다.  
> Setting 1 에서는 password를 암호화 하여 랜덤하게 생성합니다.  
> User password 를 지정 할수 없으며, 공유디렉토리가 아닌 Virtual User 별 디렉토리로 접근합니다.  



* virtual user 구성에 필요한 디렉토리를 생성 합니다.  
```no-highlight
[root@centos7 ~]# mkdir /etc/vsftpd/vconf
[root@centos7 ~]# mkdir -p /data/vftp
[root@centos7 ~]# useradd -s /sbin/nologin -d /data/vftp vsftpd
[root@centos7 ~]# chown -R vsftpd:vsftpd /data/vftp
```

* vsftpd.conf 설정  
```no-highlight
[root@centos7 ~]# vi /etc/vsftpd/vsftpd.conf
변경 
anonymous_enable=NO
#xferlog_std_format=YES
idle_session_timeout=900
nopriv_user=vsftpd
#chroot_list_enable=YES
listen=YES
listen_ipv6=NO

추가 
allow_writeable_chroot=YES
guest_enable=YES
guest_username=vsftpd
local_root=/data/vftp/$USER
user_sub_token=$USER
virtual_use_local_privs=YES
user_config_dir=/etc/vsftpd/vconf
```


* pam.d 설정  
> default vsftpd pam.d 설정 파일을 별도로 보관 합니다.  
> vsftpd pam.d 파일에 아래와 같이 설정을 합니다.  
```no-highlight
[root@centos7 ~]# cd /etc/pam.d/
[root@centos7 pam.d]# cp vsftpd vsftpd.org
[root@centos7 pam.d]# vi vsftpd
#%PAM-1.0
auth required pam_userdb.so db=/etc/vsftpd/password crypt=crypt
account required pam_userdb.so db=/etc/vsftpd/password crypt=crypt
session required pam_loginuid.so
```

* vsftpd 재시작 
```no-highlight
[root@centos7 ~]# systemctl restart vsftpd
```

* virtual ftp 사용자 생성  
```no-highlight
[root@centos7 ~]# mkdir -p /data/vftp/test1
[root@centos7 ~]# chown -R vsftpd:vsftpd /data/vftp


/etc/vsftpd/vconf/test1 파일 생성 
[root@centos7 ~]# echo 'dirlist_enable=YES
 download_enable=YES
 local_root=/data/vftp/test1
 write_enable=YES' > /etc/vsftpd/vconf/test1

test1 유저와 password 를 생성 합니다.  
[root@centos7 ~]# echo 'test1' | tee /etc/vsftpd/password{,-nocrypt} > /dev/null
[root@centos7 ~]# myval=$(openssl rand -base64 6)
[root@centos7 ~]# echo $myval >> /etc/vsftpd/password-nocrypt
[root@centos7 ~]# echo $(openssl passwd -crypt $myval) >> /etc/vsftpd/password
[root@centos7 ~]# db_load -T -t hash -f /etc/vsftpd/password /etc/vsftpd/password.db


[root@centos7 ~]# db_dump -d a /etc/vsftpd/password.db | grep len
        [000] 4090 len:   5 data: test1
        [001] 4076 len:  13 data: FCq/W4hz1lHuA
[root@centos7 ~]# cat /etc/vsftpd/password
test1
FCq/W4hz1lHuA

password-nocrypt 의 경우 사용할수 있는 password 파일 입니다.  
[root@centos7 ~]# cat /etc/vsftpd/password-nocrypt
test1
b4Ahnz0Z
[root@centos7 ~]#

```

* ftp 접속 테스트  
```no-highlight
[root@centos7 ~]# ftp localhost
Trying ::1...
ftp: connect to address ::1연결이 거부됨
Trying 127.0.0.1...
Connected to localhost (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (localhost:root): test1
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
227 Entering Passive Mode (127,0,0,1,22,107).
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
257 "/"
ftp> quit
221 Goodbye.
[root@centos7 ~]#
```

* 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --add-service=ftp
[root@centos7 ~]# firewall-cmd --reload
```


* Selinux 설정  
```no-highlight
[root@centos7 ~]# setsebool -P ftpd_full_access 1
```


* **Virtual User Setting 2**
> Virtual User Setting 2에서는 User 별 패스워드를 지정 할수 있습니다.  


- Virtual User 디렉토리  
> local_root 설정에서 Virtual User 의 디렉토리를 설정 할수 있습니다.  
> 공통 디렉토리 사용시 /data/vftp 로 설정 하시면 됩니다.  

* vsftpd.conf 설정  
```no-highlight
[root@centos7 ~]# vi /etc/vsftpd/vsftpd.conf
변경 
anonymous_enable=NO
#xferlog_std_format=YES
idle_session_timeout=900
nopriv_user=vsftpd
#chroot_list_enable=YES
listen=YES
listen_ipv6=NO

추가 
allow_writeable_chroot=YES
guest_enable=YES
guest_username=vsftpd
local_root=/data/vftp/$USER
user_sub_token=$USER
virtual_use_local_privs=YES
```

* pam.d 설정  
```no-highlight
[root@centos7 ~]# vi /etc/pam.d/vsftpd
#%PAM-1.0
auth required pam_userdb.so db=/etc/vsftpd/password
account required pam_userdb.so db=/etc/vsftpd/password
session required pam_loginuid.so
```



* vftp 디렉토리 생성  
```no-highlight
[root@centos7 ~]# mkdir -p /data/vftp/test1
[root@centos7 ~]# useradd -s /sbin/nologin -d /data/vftp vsftpd
[root@centos7 ~]# chown -R vsftpd:vsftpd /data/vftp
```

* virtual user 생성  
```no-highlight
[root@centos7 ~]# vi vftp.txt
test1 
password
test2
password
test3
password
```

* password.db 생성 및 패스워드 확인  
```no-highlight
[root@centos7 ~]# db_load -T -t hash -f /root/vftp.txt /etc/vsftpd/password.db
[root@centos7 ~]# chmod 600 /etc/vsftpd/password.db
[root@centos7 ~]# rm vftp.txt
[root@centos7 ~]# db_dump -d a /etc/vsftpd/password.db | grep len
        [000] 4090 len:   5 data: test2
        [001] 4081 len:   8 data: password
        [000] 4090 len:   5 data: test1
        [001] 4081 len:   8 data: password
        [002] 4075 len:   5 data: test3
        [003] 4066 len:   8 data: password
[root@centos7 ~]#
```

{{% notice info %}}
vftp.txt 파일 삭제  
Virtual User Setting 2 는 password 파일을 만들어 User 를 password 를 지정 합니다.  
암호화 되지 않은 password 파일은 보안상 좋지 않으며 꼭, ftp user 생성시 만든 password  파일을 삭제 해야 합니다. 
{{% /notice %}}

* 접속 확인  
```no-highlight
[root@centos7 ~]# ftp localhost
Trying ::1...
ftp: connect to address ::1연결이 거부됨
Trying 127.0.0.1...
Connected to localhost (127.0.0.1).
220 (vsFTPd 3.0.2)
Name (localhost:root): test1
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
227 Entering Passive Mode (127,0,0,1,192,110).
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
257 "/"
ftp> quit
221 Goodbye.
[root@centos7 ~]#
```


<br></br>