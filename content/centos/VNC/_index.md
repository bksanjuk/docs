+++
title = "VNC"
weight = 21
+++


## VNC

{{% notice note %}}
tigervnc 소개  
TigerVNC는 사용자가 원격 컴퓨터의 사용할수있게 해주는 클라이언트 / 서버 응용 프로그램  
VNC (Virtual Network Computing)의 고성능 플랫폼 입니다.  
TigerVNC는 3D 및 비디오 응용 프로그램을 실행하는 데 필요한 수준의 성능을 제공하며, 다양한 플랫폼에서 사용가능합니다.  
{{% /notice %}}

 
 
* tigervnc-server 설치  
```no-highlight
[root@centos7 ~]# yum install -y tigervnc-server
```

* vncserver 파일 복사  
systemd 에서 사용할 vncserver 파일을 복사 합니다.  
```no-highlight
[root@centos7 ~]# cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
```

* **vncserver 파일 수정**  

> 수정전
```no-highlight
[root@centos7 ~]# vi /etc/systemd/system/vncserver@\:1.service
[Service]
Type=forking

# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
PIDFile=/home/<USER>/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
```

> 수정후
```no-highlight
[Service]
Type=forking

# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver %i"
PIDFile=/root/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
```

* systemd 설정 reload  
vncserver 설정 변경을 적용하기 위하여 systemd 설정을 reload 합니다.  
```no-highlight
[root@centos7 ~]# systemctl daemon-reload
```


* vncserver 활성화 및 daemon 실행  
daemon 실행시 정상적으로 실행이 안될때가 있습니다.  
daemon 활성화후 system rebooting 후 확인해 보면 정상적으로 daemon 이 올라온것을 확인 할수 있습니다.  
```no-highlight
[root@centos7 ~]# systemctl enable vncserver@:1.service
Created symlink from /etc/systemd/system/multi-user.target.wants/vncserver@:1.service to /etc/systemd/system/vncserver@:1.service.
[root@centos7 ~]# systemctl start vncserver@:1.service
```

* vncuser 암호 설정  
vnc에서 사용할 암호를 설정 합니다.  
```no-highlight
[root@centos7 ~]# vncserver

You will require a password to access your desktops.

Password:
Verify:
Would you like to enter a view-only password (y/n)? y
Password:
Verify:

New 'centos7:2 (root)' desktop is centos7:2

Creating default startup script /root/.vnc/xstartup
Creating default config /root/.vnc/config
Starting applications specified in /root/.vnc/xstartup
Log file is /root/.vnc/centos7:2.log

[root@centos7 ~]#
```

* 방화벽 설정  
```no-highlight
[root@centos7 ~]# firewall-cmd --permanent --zone=public --add-service vnc-server
success
[root@centos7 ~]# firewall-cmd --reload
success
[root@centos7 ~]#
```

* vncserver 동작확인  
```no-highlight
[root@centos7 ~]# systemctl status vncserver@:1.service
● vncserver@:1.service - Remote desktop service (VNC)
   Loaded: loaded (/etc/systemd/system/vncserver@:1.service; enabled; vendor preset: disabled)
   Active: active (running) since 월 2018-10-15 09:19:56 KST; 6min ago
  Process: 1005 ExecStart=/usr/sbin/runuser -l root -c /usr/bin/vncserver %i (code=exited, status=0/SUCCESS)
  Process: 988 ExecStartPre=/bin/sh -c /usr/bin/vncserver -kill %i > /dev/null 2>&1 || : (code=exited, status=0/SUCCESS)
 Main PID: 1097 (Xvnc)
   CGroup: /system.slice/system-vncserver.slice/vncserver@:1.service
           ‣ 1097 /usr/bin/Xvnc :1 -auth /root/.Xauthority -desktop centos7:1 (root) -fp catalogue:/etc/X11/fontpath.d -geometry 1024x768 -pn -rfbauth /root/.vnc/passwd -rfbport 5901 -rfbwait...

10월 15 09:19:53 centos7 systemd[1]: Starting Remote desktop service (VNC)...
10월 15 09:19:56 centos7 systemd[1]: Started Remote desktop service (VNC).
[root@centos7 ~]#
```

> vnc Client 는 [tigervnc.org](https://tigervnc.org/) releases 에서 최신 버젼을 다운 받을수 있습니다.  

<br></br>

* **vncserver 접속**  

![vnc1](/centos/centos_tmp/install/k16.png)  

> tigervnc.org 에서 vncViewer 를 다운 받아 접속 할수 있습니다.  
> vnc의 경우 5901 port 를 사용 합니다.  
> vncserverIP:1 번으로 접속 합니다.  



![vnc2](/centos/centos_tmp/install/k18.png)  

{{% notice tip %}}
vnc-server 비정상 작동시  
vnc-server 가 정상적으로 작동 하지 않을시  
/root/.vnc 디렉토리의 .log .pid 파일 삭제  
/tmp/.X11-unix 디렉토리의 X로 시작하는 파일을 삭제 합니다.  
{{% /notice %}}

> example  
```no-highlight
[root@centos7 ~]# cd .vnc/
[root@centos7 .vnc]# ls
centos7:1.log  centos7:1.pid  config  passwd  xstartup
[root@centos7 .vnc]# rm -rf centos7\:1.*
[root@centos7 tmp]# rm -rf .X11-unix/
```

<br></br>