+++
title = "Install"
weight = 1
+++

## Quick install


> CentOS7 Quick install  
```
[root@CentOS7 ~]# yum install -y yum-utils device-mapper-persistent-data lvm2
[root@CentOS7 ~]# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
[root@CentOS7 ~]# yum install -y docker-ce
[root@CentOS7 ~]# sudo usermod -aG docker test
[root@CentOS7 ~]# systemctl enable docker
[root@CentOS7 ~]# sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
[root@CentOS7 ~]# sudo chmod +x /usr/local/bin/docker-compose
```

> Docker 특정버전 설치  
```
[root@CentOS7 ~]# curl https://releases.rancher.com/install-docker/18.06.sh | sh
```

<br></br>
## Binary install


> wget 을 이용하여 docker 설치 파일을 다운로드 합니다. 
```no-highlight
test@ubuntu1604:~$ wget https://download.docker.com/linux/static/stable/x86_64/docker-17.12.0-ce.tgz
--2018-02-05 18:21:58--  https://download.docker.com/linux/static/stable/x86_64/docker-17.12.0-ce.tgz
Resolving download.docker.com (download.docker.com)... 54.192.183.142, 54.192.183.217, 54.192.183.121, ...
Connecting to download.docker.com (download.docker.com)|54.192.183.142|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 34272897 (33M) [application/x-tar]
Saving to: ‘docker-17.12.0-ce.tgz’

docker-17.12.0-ce.t 100%[===================>]  32.68M  13.4MB/s    in 2.4s

2018-02-05 18:22:01 (13.4 MB/s) - ‘docker-17.12.0-ce.tgz’ saved [34272897/34272897]

test@ubuntu1604:~$
```

> 압축풀기
```no-highlight
test@ubuntu1604:~$ tar xvf docker-17.12.0-ce.tgz
docker/
docker/docker-containerd-shim
docker/docker-containerd
docker/docker-runc
docker/docker
docker/docker-init
docker/docker-containerd-ctr
docker/docker-proxy
docker/dockerd
test@ubuntu1604:~$
```

> Docker 파일 카피
```no-highlight
test@ubuntu1604:~$ sudo cp docker/* /usr/bin/
```

> Docker 실행
```no-highlight
test@ubuntu1604:~$ sudo dockerd &
~중략
test@ubuntu1604:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED                                                                                                                                                                                                            STATUS              PORTS               NAMES
test@ubuntu1604:~$
```

> docker 사용자 등록
```no-highlight
test@ubuntu1604:~$ sudo usermod -aG docker test
```

> Systemd Scripts 생성
```no-highlight
sanjuk@docker-test:~$ cat /lib/systemd/system/docker.service
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service
Wants=network-online.target
Requires=docker.socket

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd://
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=1048576
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
```

> systemctl docker enable
> 모든 설정을 완료한뒤 테스트를 위하여 시스템을 재기동 합니다.  
```no-highlight
test@ubuntu1604:~$ sudo -i
[sudo] password for test:
root@ubuntu1604:~# docker ps
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
root@ubuntu1604:~# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /lib/systemd/system/docker.service.
root@ubuntu1604:~# init 6
```

> **docker Test**  
> 도커 테스트를 위하여 ubuntu 16.04 버젼을 image 를 실행해 봅니다.  
> docker run 실행시 -it 옵션을 하여 도커 내부로 접속 합니다.  
> -rm 옵션의 경우 도커 외부로 빠져나오면 컨테이너 실행이 정지 하게 됩니다.  
```no-highlight
sanjuk@ubuntu1604:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
sanjuk@ubuntu1604:~$ docker run ubuntu:16.04
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
1be7f2b886e8: Pull complete
6fbc4a21b806: Pull complete
c71a6f8e1378: Pull complete
4be3072e5a37: Pull complete
06c6d2f59700: Pull complete
Digest: sha256:e27e9d7f7f28d67aa9e2d7540bdc2b33254b452ee8e60f388875e5b7d9b2b696
Status: Downloaded newer image for ubuntu:16.04
sanjuk@ubuntu1604:~$ docker run --rm -it ubuntu:16.04 /bin/bash
root@cd21ecd3c368:/# uname -a
Linux cd21ecd3c368 4.4.0-62-generic #83-Ubuntu SMP Wed Jan 18 14:10:15 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
root@cd21ecd3c368:/# exit
```
<br></br>

## Scripts install

> get.docker.com 에서 제공하는 스크립트를 이용하여 간단히 설치 할수 있습니다.  

> **Docker 설치**  
> usermode -aG docker $user-name 를 통하여 유저권한을 추가해야 합니다.  
```no-highlight
test@ubuntu-docs:~$ curl -s https://get.docker.com/ | sudo sh
test@ubuntu-docs:~$ sudo usermod -aG docker test
```


<br></br>

## CentOS Docker install  

> required package 설치  
```no-highlight
[root@centos7 ~]# sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
```

> repositori 추가  
```no-highlight
[root@centos7 ~]#  sudo yum-config-manager \
     --add-repo \
     https://download.docker.com/linux/centos/docker-ce.repo
```

> **repolist 확인**  
> docker-ce-stable repo 추가 확인 합니다.  
```no-highlight
[root@centos7 ~]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.kakao.com
 * extras: mirror.kakao.com
 * updates: mirror.kakao.com
repo id                             repo name                             status
base/7/x86_64                       CentOS-7 - Base                       9,911
docker-ce-stable/x86_64             Docker CE Stable - x86_64                20
extras/7/x86_64                     CentOS-7 - Extras                       432
updates/7/x86_64                    CentOS-7 - Updates                    1,540
repolist: 11,903
[root@centos7 ~]#
```

> yum install docker  
```no-highlight
[root@centos7 ~]# yum install -y docker-ce
```

> systemctl docker 활성화 및 docker start  
```no-highlight
[root@centos7 ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@centos7 ~]# systemctl start docker
```

> docker test
```no-highlight
[root@centos7 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@centos7 ~]# docker run --rm -it ubuntu:16.04 /bin/bash
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
3b37166ec614: Pull complete
504facff238f: Pull complete
ebbcacd28e10: Pull complete
c7fb3351ecad: Pull complete
2e3debadcbf7: Pull complete
Digest: sha256:45ddfa61744947b0b8f7f20b8de70cbcdd441a6a0532f791fd4c09f5e491a8eb
Status: Downloaded newer image for ubuntu:16.04
root@e5010e637a43:/# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.5 LTS"
root@e5010e637a43:/#
```




<br></br>

## Ubuntu Docker install  


> Docker 설치전 apt update 를 실행합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get update
```

> https 저장소를 사용하기 위하여 패키지를 설치 합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     software-properties-common
```


> Docker 공식 GPG key 값 추가  

```no-highlight
test@ubuntu:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

> apt 저장소를 등록 합니다.  
```no-highlight
test@ubuntu:~$  sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

> docker 설치전 apt update 를 진행합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get update
```

> Docker 설치  
```no-highlight
test@ubuntu:~$ sudo apt-get install docker-ce
```

> User 권한 추가  
```no-highlight
test@ubuntu:~$ sudo usermod -aG docker test
```

> ssh client 재접속후  docker ps 명령어를 실행 합니다.  
```no-highlight
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
test@ubuntu:~$
```

> Docker test
```no-highlight
test@ubuntu:~$ docker run --rm -it ubuntu:16.04 /bin/bash
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
3b37166ec614: Pull complete
504facff238f: Pull complete
ebbcacd28e10: Pull complete
c7fb3351ecad: Pull complete
2e3debadcbf7: Pull complete
Digest: sha256:45ddfa61744947b0b8f7f20b8de70cbcdd441a6a0532f791fd4c09f5e491a8eb
Status: Downloaded newer image for ubuntu:16.04
root@d3fabfa958c7:/#
```
<br></br>



