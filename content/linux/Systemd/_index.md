+++
title = "Systemd"
weight = 6
+++


## Systemd  

* What is Systemd

{{% notice note %}}
Systemd는 Linux 운영 체제용 시스템 및 서비스 관리자입니다.  
SysV init 스크립트와 역 호환되도록 설계되었으며 부팅시 시스템 서비스의 병렬 시작, 데몬의 활성화 및 dependency 기반 서비스 제어 와 같은 여러 기능을 제공합니다. 
{{% /notice %}}

* Run Level 변경

{{% notice info %}}
CentOS 7 이전 버젼의 경우 /etc/inittab 을 변경하여 Run Level 을 변경 하였지만 Systemd 이용시 Command 로 Run Level 을 변경 할수 있습니다. 
{{% /notice %}}


|Run Level    |Target    |
|---|---|
|Run Level 0   |poweroff.target   |
|Run Level 1   |rescue.target   |
|Run Level 3   |multi-user.target   |
|Run Level 5   |graphical.target   |
|Run Level 6   |reboot.target   |
|Emergency   |emergency.target   |


* Run Level 확인 

```no-highlight
[root@centos7 ~]# systemctl get-default
multi-user.target
```

* System 동작중 Run Level 변경  
Run Level 만 변경 하며 리부팅시 설정된 Run Level 로 돌아 옵니다.  
```no-highlight
[root@centos7 ~]# systemctl isolate graphical.target
```

* Run Level 변경  
```no-highlight
[root@centos7 ~]# systemctl set-default graphical.target
or  
[root@centos7 ~]# systemctl set-default multi-user.target
```

**주요 systemd Command**  

- Daemon 활성화  
```no-highlight
[root@centos7 ~]# systemctl enable httpd
```

- Daemon 실행  
```no-highlight
[root@centos7 ~]# systemctl start httpd
```

- Daemon 재시작  
```no-highlight
[root@centos7 ~]# systemctl restart httpd
```

- Daemon 상태 확인  
```no-highlight
[root@centos7 ~]# systemctl status httpd
```

- Daemon 정지  
```no-highlight
[root@centos7 ~]# systemctl stop httpd
```

- Daemon 비 활성화  
```no-highlight
[root@centos7 ~]# systemctl disable httpd
```

- Daemon 의존성 확인  
```no-highlight
[root@centos7 ~]# systemctl list-dependencies
```

- Daemon 활성화 상태 확인  
```no-highlight
[root@centos7 ~]# systemctl is-enabled httpd
disabled
```

{{% notice note %}}
Daemon mask 의 경우 활성화 비활성화 가 아닌 mask 처리를 해놓아서 unmask 하기 전까지 사용할수 없게 만듭니다.  
{{% /notice %}}


- Daemon mask
```no-highlight
[root@centos7 ~]# systemctl is-enabled httpd
masked

[root@centos7 ~]# systemctl start httpd
Failed to start httpd.service: Unit is masked.


[root@centos7 ~]# systemctl unmask httpd
Removed symlink /etc/systemd/system/httpd.service.

[root@centos7 ~]# systemctl start httpd
```

- Daemon 구성 확인  
```no-highlight
[root@centos7 ~]# systemctl show httpd
Type=notify
Restart=no
NotifyAccess=main
RestartUSec=100ms
TimeoutStartUSec=1min 30s
TimeoutStopUSec=1min 30s
WatchdogUSec=0
WatchdogTimestampMonotonic=0
StartLimitInterval=10000000
StartLimitBurst=5
StartLimitAction=none
FailureAction=none
PermissionsStartOnly=no
RootDirectoryStartOnly=no
RemainAfterExit=no
GuessMainPID=yes
MainPID=0
ControlPID=0
FileDescriptorStoreMax=0
StatusErrno=0
Result=success
ExecMainStartTimestampMonotonic=0
ExecMainExitTimestampMonotonic=0
lines 1-23...skipping...
``` 중략
```

- Daemon 서비스 구성 확인  
```no-highlight
[root@centos7 ~]# systemctl cat httpd
# /usr/lib/systemd/system/httpd.service
[Unit]
Description=The Apache HTTP Server
After=network.target remote-fs.target nss-lookup.target
Documentation=man:httpd(8)
Documentation=man:apachectl(8)

[Service]
Type=notify
EnvironmentFile=/etc/sysconfig/httpd
ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND
ExecReload=/usr/sbin/httpd $OPTIONS -k graceful
ExecStop=/bin/kill -WINCH ${MAINPID}
# We want systemd to give httpd some time to finish gracefully, but still want
# it to kill httpd after TimeoutStopSec if something went wrong during the
# graceful stop. Normally, Systemd sends SIGTERM signal right after the
# ExecStop, which would kill httpd. We are sending useless SIGCONT here to give
# httpd time to finish.
KillSignal=SIGCONT
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

{{% notice info %}}
Systemd 설정 변경시 systemctl daemon-reload 명령어를 이용하여 변경된 설정을 적용 할수 있습니다.  
{{% /notice %}}


- Systemd Reload
```no-highlight
[root@centos7 ~]# systemctl daemon-reload
```

- 모든 Daemon 상황 확인  
```no-highlight
[root@centos7 ~]# systemctl list-unit-files
```

- rescue mode

{{% notice note %}}
systemctl rescue 모드 실행시 아래와 같이 바로 rescue mode 로 진입 하며 콘솔에서 확인시 maintenance mode 로 빠지는것을 확인 할수 있습니다. 
{{% /notice %}}

```no-highlight
[root@centos7 ~]# systemctl rescue
PolicyKit daemon disconnected from the bus.
We are no longer a registered authentication agent.

Broadcast message from root@centos7 on pts/0 (Wed 2018-11-14 01:49:50 KST):

The system is going down to rescue mode NOW!
```

- rescue mode 종료  
```no-highlight
[root@centos7 ~]# systemctl reboot
```



<br></br>

<br></br>