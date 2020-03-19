+++
title = "Docker Machine"
weight = 9
+++



## Docker Machine  

![docker14](/docker/docker_tmp/D14.png)

{{% notice note %}}
**Docker Machine 소개**  
Docker Machine은 가상 호스트에 Docker Engine을 설치하고 docker-machine 명령으로  
호스트를 관리 할 수있게 해주는 도구입니다.  
docker-machine 명령을 사용하여 관리 대상 호스트를 시작, 검사, 중지 및 재시작하고  
Docker 클라이언트 및 데몬을 업그레이드하고 호스트와 통신하도록 Docker 클라이언트를 구성 할 수 있습니다.  
**문서에는 node 라고 표현하였으나 vm 이 정확한 표현 입니다.**  
{{% /notice %}}


> **docker machine install**  
> [docker-machine설치참고 Site](https://docs.docker.com/machine/install-machine/)  
> 문서의 경우 windows 10 환경에 vmware Workstaion 환경의 설치를 기준으로 작성 합니다.  
> [vmware Workstaion 설치 참고 Site](https://github.com/pecigonzalo/docker-machine-vmwareworkstation)  
> 설치 참고 Site 에서 확인한 결과 docker tools 를 먼저 설치 해야 됩니다.  
> docker tools 는  [dokcer-tools](https://docs.docker.com/toolbox/toolbox_install_windows/) 에서 다운 받을수 있습니다.  

> **Git windows 에서 설치**  
```no-highlight
base=https://github.com/docker/machine/releases/download/v0.14.0 &&
  mkdir -p "$HOME/bin" &&
  curl -L $base/docker-machine-Windows-x86_64.exe > "$HOME/bin/docker-machine.exe" &&
  chmod +x "$HOME/bin/docker-machine.exe"
```

> 설치후 C:\Program Files\Docker Toolbox 디렉토리에 반드시 docker-machine-driver-vmwareworkstation.exe 파일이 있어야 합니다.  
> docker-machine-driver-vmwareworkstation.exe 파일은 [site](https://github.com/pecigonzalo/docker-machine-vmwareworkstation/releases) 에서 다운 받을수 있습니다.  
> docker tools 를 설치 하면 윈도우즈 바탕화면에 Docker Quickstart Terminal 아이콘이 생깁니다.  
> 클릭을 하여 docker-machine 버젼을 확인 합니다.  

```no-highlight
test@DESKTOP-MLON2R1 MINGW64 ~
$ docker-machine version
docker-machine.exe version 0.14.0, build 89b8332

test@DESKTOP-MLON2R1 MINGW64 ~
$
```


> docker-machine 의 경우 기본적으로 Virtualbox 를 사용 합니다.  
> docker-machine 을 이용하여 node1 을 생성 합니다.  
```no-highlight
$ docker-machine create node1
Running pre-create checks...
Creating machine...
(node1) Copying C:\Users\test\.docker\machine\cache\boot2docker.iso to C:\Users\test\.docker\machine\machines\node1\boot2docker.iso...
(node1) Creating VirtualBox VM...
(node1) Creating SSH key...
(node1) Starting the VM...
(node1) Check network to re-create if needed...
(node1) Windows might ask for the permission to configure a dhcp server. Sometimes, such confirmation window is minimized in the taskbar.
(node1) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...

This machine has been allocated an IP address, but Docker Machine could not
reach it successfully.

SSH for the machine should still work, but connecting to exposed ports, such as
the Docker daemon port (usually <ip>:2376), may not work properly.

You may need to add the route manually, or use another related workaround.

This could be due to a VPN, proxy, or host file configuration issue.

You also might want to clear any VirtualBox host only interfaces you are not using.
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: C:\Users\test\bin\docker-machine.exe env node1

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> docker-machine 으로 생성한 node 를 확인 합니다.  
> docker-machine create 사용시 아무런 옵션이 없으면 virtualbox 드라이버를 이용하여 node 를 생성 합니다.  

```no-highlight
test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                         SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376             v18.06.1-ce
node1     -        virtualbox          Running   tcp://192.168.99.100:2376           v18.06.1-ce

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> vmwareworkstation Driver 를 이용하여 node 를 생성합니다.  

```no-highlight
$ docker-machine create --driver=vmwareworkstation node2
Running pre-create checks...
Creating machine...
(node2) Copying C:\Users\test\.docker\machine\cache\boot2docker.iso to C:\Users\test\.docker\machine\machines\node2\boot2docker.iso...
(node2) Creating SSH key...
(node2) Creating VM...
(node2) Creating disk 'C:\Users\test\.docker\machine\machines\node2\node2.vmdk'
(node2) Virtual disk creation successful.
(node2) Starting node2...
(node2) Waiting for VM to come online...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: C:\Users\test\bin\docker-machine.exe env node2

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> **docker-machine rm 명령어**  
> docker-machine 으로 생성한 node 를 삭제 할때 사용 하는 명령어 입니다.  

```no-highlight
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                         SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376             v18.06.1-ce
node1     -        virtualbox          Running   tcp://192.168.99.100:2376           v18.06.1-ce
node2     -        vmwareworkstation   Running   tcp://192.168.0.13:2376             v18.06.1-ce

test@DESKTOP-MLON2R4 MINGW64 ~
$
$ docker-machine rm node1
About to remove node1
WARNING: This action will delete both local reference and remote instance.
Are you sure? (y/n): y
Successfully removed node1

test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine rm node2
About to remove node2
WARNING: This action will delete both local reference and remote instance.
Are you sure? (y/n): y
(node2) Deleting node2...
Successfully removed node2

test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                       SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376           v18.06.1-ce

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> **docker-machine ssh 명령어**  
> docker-mahicne 으로 생성한 node 의 경우 기본적으로 ssh 연결이 가능합니다.  
> password 파일이 아닌 ssh key 값으로 접속을 진행 합니다. docker-machine ssh node1 등으로 접속하시면 됩니다.  
> 외부 ssh 접속시 user:docker / password: tcuser 로 접속 할수 있습니다.  

<br></br>  

> **vmware driver 에서 사용할수 있는 옵션은 다음과 같습니다.**  

|CLI option   	|Environment variable   	|Default   	|
|---	|---	|---	|
|--vmwareworkstation-boot2docker-url   	|WORKSTATION_BOOT2DOCKER_URL   	|Latest boot2docker url   	|
|--vmwareworkstation-cpu-count   	|WORKSTATION_CPU_COUNT   	|1   	|
|--vmwareworkstation-disk-size   	|WORKSTATION_DISK_SIZE   	|20000   	|
|--vmwareworkstation-memory-size   	|WORKSTATION_MEMORY_SIZE	   	|1024   	|
|--vmwareworkstation-ssh-user   	|WORKSTATION_SSH_USER   	|docker   	|
|--vmwareworkstation-ssh-password   	|WORKSTATION_SSH_PASSWORD   	|tcuser   	|



```no-highlight
$ docker-machine ssh node1
                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
Boot2Docker version 18.06.1-ce, build HEAD : c7e5c3e - Wed Aug 22 16:27:42 UTC 2018
Docker version 18.06.1-ce, build e68fc7a
docker@node1:~$
```

> **docker-machine stop 명령어**  
> docker-machine 으로 생성된 node 를 정지 할때 사용합니다.  
```no-highlight
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                       SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376           v18.06.1-ce
master    -        vmwareworkstation   Running   tcp://192.168.0.15:2376           v18.06.1-ce
node1     -        vmwareworkstation   Running   tcp://192.168.0.14:2376           v18.06.1-ce
node2     -        vmwareworkstation   Running   tcp://192.168.0.13:2376           v18.06.1-ce

test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine stop node2
Stopping "node2"...
Machine "node2" was stopped.

test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                       SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376           v18.06.1-ce
master    -        vmwareworkstation   Running   tcp://192.168.0.15:2376           v18.06.1-ce
node1     -        vmwareworkstation   Running   tcp://192.168.0.14:2376           v18.06.1-ce
node2     -        vmwareworkstation   Stopped                                     Unknown

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> **docker-machine start 명령어**  
> docker-mcahine node 를 실행 시킬때 사용합니다.  
```no-highlight
$ docker-machine start node2
Starting "node2"...
Machine "node2" was started.
Waiting for SSH to be available...
Detecting the provisioner...
Started machines may have new IP addresses. You may need to re-run the `docker-machine env` command.

test@DESKTOP-MLON2R4 MINGW64 ~
$ docker-machine ls
NAME      ACTIVE   DRIVER              STATE     URL                       SWARM   DOCKER        ERRORS
default   *        vmwareworkstation   Running   tcp://192.168.0.12:2376           v18.06.1-ce
master    -        vmwareworkstation   Running   tcp://192.168.0.15:2376           v18.06.1-ce
node1     -        vmwareworkstation   Running   tcp://192.168.0.14:2376           v18.06.1-ce
node2     -        vmwareworkstation   Running   tcp://192.168.0.13:2376           v18.06.1-ce

test@DESKTOP-MLON2R4 MINGW64 ~
$
```

> **docker-machine env 명령어**  

```no-highlight
$ docker-machine env node1
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.0.14:2376"
export DOCKER_CERT_PATH="C:\Users\test\.docker\machine\machines\node1"
export DOCKER_MACHINE_NAME="node1"
export COMPOSE_CONVERT_WINDOWS_PATHS="true"
# Run this command to configure your shell:
# eval $("C:\Users\test\bin\docker-machine.exe" env node1)
test@DESKTOP-MLON2R4 MINGW64 ~
$
```