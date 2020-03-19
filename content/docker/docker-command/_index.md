+++
title = "Docker Command"
weight = 4
+++


## Docker Comamand  

{{% notice note %}}
Docker 명령어 사용법을 정리 합니다.  
기본으로 사용하는 명령어 위주로 사용하시고 필요할 경우 찾아 보시는것이 좋습니다.  
{{% /notice %}}

{{% notice info %}}
docker Command 참고  
$ docker Command Option 형식으로 사용합니다.  
[Docker 명령어 Site ](https://docs.docker.com/engine/reference/commandline/cli/)  
{{% /notice %}}

> **docker help 명령어 확인**  

```no-highlight
test@ubuntu:~$ docker help

Usage:  docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/home/test/.docker")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/home/test/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/home/test/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/home/test/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  config      Manage Docker configs
  container   Manage containers
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.
test@ubuntu:~$
```
* **docker attach**  

{{% notice note %}}
docker attach
실행중인 컨테이너에 로컬 표준 입력, 출력 및 오류 스트림 첨부  
{{% /notice %}}


> 일반적으로 컨테이너에 접속 하기 위한 명령은 docker exec -it $컨테이너id /bin/bash 를 사용 합니다.  


> **docker attach example**  
> 컨테이너를 실행 하고 docker attach 를 이용하여 컨테이너에 접속 합니다.  
> 컨테이너에서 빠져 나올때는 Ctrl + p q 로 빠져 나옵니다.  
> 컨테이너에서 빠져 나올때  Ctrl + d 로 빠져 나올 경우 컨테이너는 정지 합니다.  
> docker ps 로 컨테이너 구동상태를 확인 합니다.  
```no-highlight
$ docker run --name test -d -it debian
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
05d1a5232b46: Pull complete
Digest: sha256:07fe888a6090482fc6e930c1282d1edf67998a39a09a0b339242fbfa2b602fff
Status: Downloaded newer image for debian:latest
8e14f3f3d4ffaad6bede08bfd076a3d07309ce646915cfe339f715261c94da90
test@ubuntu:~/Workspace/Docker-test/test01$ docker attach test
root@8e14f3f3d4ff:/#
root@8e14f3f3d4ff:/#
root@8e14f3f3d4ff:/#
root@8e14f3f3d4ff:/# read escape sequence
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
8e14f3f3d4ff        debian              "bash"              About a minute ago   Up About a minute                       test
$
```  
> docker attach 로 컨테이너에 접속 하기 위해서는 docker run -d -it 옵션으로 컨테이너를 실행 해야 합니다.  
> -d 컨테이너를 생성하여 백그라운드에서 실행  
> -t 컨테이너 표준입력 열기  
> -t tty 사용  


* **docker build**  


{{% notice note %}}
docker build
docker build 는 Dockerfile 을 이용하여 image 생성시 사용합니다.  
{{% /notice %}}



> **docker build example**  
> Dockerfile 을 이용하여 image 를 생성 합니다.  
> docker ps 명령어로 만들어진 이미지를 확인 합니다.  
```no-highlight
test@ubuntu:~/Workspace/test01$ vi Dockerfile
FROM ubuntu:16.04
MAINTAINER test <test@anonymouse.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx


VOLUME /data

WORKDIR /etc/nginx

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
test@ubuntu:~/Workspace/test01$ docker build --tag nginx . 

test@ubuntu:~/Workspace/test01$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              10608766ca7a        6 seconds ago       212MB
test@ubuntu:~/Workspace/test01$

```

> **docker commit**  

{{% notice note %}}
docker commit  
컨테이너 변경사항을 적용한 새로운 이미지를 만드는 작업 입니다.  
{{% /notice %}}

> **docker commit example**  
> 기존 ssh-server 컨테이너에 접속을 하여 git 패키지를 설치 합니다.  
> docker commit 명령어로 git 이 포함된 image를 생성 합니다.  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                  PORTS                                                      NAMES
eff6ba796e43        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   5 seconds ago       Up Less than a second   0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
189479f8c9aa        ssh-server            "/usr/sbin/sshd -D"      5 seconds ago       Up 3 seconds            0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker exec -it ssh-server /bin/bash
root@189479f8c9aa:/# apt-get install -y git


root@189479f8c9aa:/# exit 

test@ubuntu:~/Workspace/ftp-server$ docker commit ssh-server ssh-server:git
sha256:694b90275abfc328f3f5a5c370b280f341dfec46ca5ddab2c7ad38c0b0f27c75
test@ubuntu:~/Workspace/ftp-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ssh-server          git                 694b90275abf        9 seconds ago       286MB   <--- TAG 확인시 docker commit 을 한 git ssh-server 이미지를 확인할수 있습니다. 
nginx               latest              10608766ca7a        21 hours ago        212MB
ftp/pure-ftpd       14.04               05b750504fe5        2 days ago          401MB
ssh-server          latest              212d3f646e97        2 days ago          211MB
ubuntu              16.04               b9e15a5d1e1a        4 weeks ago         115MB
ubuntu              14.04               c32fae490809        4 weeks ago         188MB
ubuntu              latest              cd6d8154f1e1        4 weeks ago         84.1MB
debian              latest              f2aae6ff5d89        4 weeks ago         101MB
test@ubuntu:~/Workspace/ftp-server$  
```  


> **docker cp**  

{{% notice note %}}
docker cp  
컨테이너의 파일을 카피할때 사용합니다.  
{{% /notice %}}



> **docker cp example**  
> pure-ftpd 사용자 생성후 pureftpd.password 파일을 로컬 pure-ftpd 설정디렉토리로 카피 합니다.  

```no-highlight  
docker ps 명령어로 ftpd 의 컨테이너 ID 를 확인 합니다.  
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
eff6ba796e43        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   15 minutes ago      Up 15 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
189479f8c9aa        ssh-server            "/usr/sbin/sshd -D"      15 minutes ago      Up 15 minutes       0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker exec -it eff6ba796e43 /bin/bash
root@eff6ba796e43:/# pure-pw useradd test1 -u ftpd-data -g ftpd-data -d /home/ftp
Password:
Enter it again:
root@eff6ba796e43:/# pure-pw mkdb
root@eff6ba796e43:/# exit
test@ubuntu:~/Workspace/ftp-server$ cd pure-ftpd/
test@ubuntu:~/Workspace/ftp-server/pure-ftpd$ ls
Dockerfile  pureftpd.passwd
test@ubuntu:~/Workspace/ftp-server/pure-ftpd$ docker cp eff6ba796e43:/etc/pure-ftpd/pureftpd.passwd .
test@ubuntu:~/Workspace/ftp-server/pure-ftpd$ cat pureftpd.passwd
test:$1$P4DB31/0$iIPf8.YJ6XP6k1zsmQtJq1:1000:1000::/home/ftp/./::::::::::::
test1:$1$na2YwOw0$vXXwmKm1Vo014M5oRpGyu/:1000:1000::/home/ftp/./::::::::::::
test@ubuntu:~/Workspace/ftp-server/pure-ftpd$
```

> **docker create**  

{{% notice note %}}
docker create  
docker create 는 컨테이너 생성시 사용합니다.  
docker run 의 경우 컨테이너 생성후 실행을 하지만 docker create 는 컨테이너 생성만 합니다.  
{{% /notice %}}

> **docker create example**  
> 컨테이너를 생성후 docker ps 명령어로 컨테이너 실행 상태를 확인 합니다.  

```no-highlight  
test@ubuntu:~/Workspace$ docker create -it --name web-service httpd /bin/bash
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
f189db1b88b3: Pull complete
ba2d31d4e2e7: Pull complete
23a65f5e3746: Pull complete
5e8eccbd4bc6: Pull complete
4c145eec18d8: Pull complete
c64fd93430a0: Pull complete
c9efc31823a2: Pull complete
Digest: sha256:81bc5f68f994a3c7bffc5d6ecba9e4fde70488c43ee8d57846a45c4995c67a23
Status: Downloaded newer image for httpd:latest
130e40719b40f5d08a21a8bf5361fe2ec69d52a338e2e7b68e10c9f96395c727
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
test@ubuntu:~/Workspace$
```


> **docker diff**  

{{% notice note %}}
docker diff  
docker diff 명령어는 컨테이너에서 변경된 파일을 확인할때 사용합니다.  
{{% /notice %}}

|심볼   	|설명    	|
|---	|---	|
|A   	|추가된 파일    	|
|D   	|삭제된 파일    	|
|C   	|변경된 파일   	|  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
e573cd7dca0b        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   16 seconds ago      Up 9 seconds        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
45cd7a7269fd        ssh-server            "/usr/sbin/sshd -D"      16 seconds ago      Up 14 seconds       0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker diff e573cd7dca0b
C /run
A /run/pure-ftpd.pid
test@ubuntu:~/Workspace/ftp-server$ docker diff 45cd7a7269fd
C /home
C /home/test
A /home/test/ftp
C /run
A /run/sshd.pid
test@ubuntu:~/Workspace/ftp-server$
```  


> **docker events**  


{{% notice note %}}
docker events
docker events 는 리얼타임으로 이벤트 확인시 사용합니다.  
{{% /notice %}}


> **docker events example**  
> ssh 터미널 2대에서 컨테이너 실행 / 컨테이너 이벤트를 확인 합니다.  
> 터미널 1에서 docker events 명령어를 실행 합니다.  
```no-highlight
test@ubuntu:~$ docker events
2018-10-06T16:09:42.490532152+09:00 network create 0830cf7d056864010175fcb5dd6a7311ce4b2e28bb1f7758ea9365a0926100c4 (name=ftpserver_default, type=bridge)
2018-10-06T16:09:42.586646157+09:00 image tag sha256:212d3f646e97afe608dde49220e62e08c9d743062863b66e1c63103822eb26df (name=ssh-server:latest)
2018-10-06T16:09:42.691771402+09:00 image tag sha256:9cae9b363b5a8cb0dc5b58ab43fd9cb6357a0011ad4cf2902f20a2f918b30520 (name=ftp/pure-ftpd:14.04)
2018-10-06T16:09:42.861839801+09:00 container create 404ad93a5840cb3e379275cb64b4297d8238013771bd1f1532cb93ae78328392 (com.docker.compose.config-hash=e922fb1d53e3afd3b1e24ba8ed07b4d78f445e06d6037004800d89d7edd7d850, com.docker.compose.container-number=1, com.docker.compose.oneoff=False, com.docker.compose.project=ftpserver, com.docker.compose.service=ssh-server, com.docker.compose.version=1.19.0, image=ssh-server, name=ssh-server)
```

> 터미널 2에서 docker-compose 를 실행 합니다.  
```no-highlight  
test@ubuntu:~/Workspace/ftp-server$ docker-compose up -d --build
```


> **docker exec**  

{{% notice note %}}
docker exec  
docker exec 명령어는 실행중인 컨테이너에 접속할때 사용합니다.  
{{% /notice %}}


> **docker exec example**  
> docker ps 명령어로 컨테이너ID 를 확인 합니다.  
> docker exec 명령어로 실행중인 컨테이너에 접속 합니다.  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
6e97f63d80fa        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   3 minutes ago       Up 3 minutes        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
404ad93a5840        ssh-server            "/usr/sbin/sshd -D"      3 minutes ago       Up 3 minutes        0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker exec -it 6e97f63d80fa /bin/bash
root@6e97f63d80fa:/#
```  

> **docker export**  

{{% notice note %}}
docker export
docker export 는 컨테이너 파일시스템을 tar 압축으로 만듭니다.  
{{% /notice %}}

> docker 백업의 경우 차후 설명 하도록 하겠습니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker export ssh-server > ./ssh-server.tar
test@ubuntu:~/Workspace/ftp-server$ ls
docker-compose.yml  pure-ftpd  ssh-server  ssh-server.tar
test@ubuntu:~/Workspace/ftp-server$
```


> **docker import**  

{{% notice note %}}
docker import
docker import 는 export 한 tar 파일로 image를 생성 합니다.  
{{% /notice %}}

* docker import example  
docker export 로 생성한 ssh-server.tar 파일에서 ssh-server image 를 생성합니다.  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ cat ssh-server.tar | docker import - ssh-server
sha256:e18ecfd3f50a110175be67d5426acf1177ed79c25991d76da3fc04b00ee59e27
test@ubuntu:~/Workspace/ftp-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ssh-server          latest              e18ecfd3f50a        6 seconds ago       181MB
test@ubuntu:~/Workspace/ftp-server$
```

> **docker history**  

{{% notice note %}}
docker history  
docker history 는 컨테이너의 history 확인시 사용합니다.  
{{% /notice %}}


> docker history example  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker history ssh-server
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
212d3f646e97        2 days ago          /bin/sh -c #(nop)  CMD ["/usr/sbin/sshd" "-D…   0B
d8289a15c096        2 days ago          /bin/sh -c #(nop)  EXPOSE 22                    0B
94680df1ab14        2 days ago          /bin/sh -c echo "export VISIBLE=now" >> /etc…   594B
c0315bd26cd4        2 days ago          /bin/sh -c #(nop)  ENV NOTVISIBLE=in users p…   0B
cd9ea05ee59b        2 days ago          /bin/sh -c apt-get update && apt-get install…   95.8MB
c1d4c20408a7        2 days ago          /bin/sh -c #(nop)  MAINTAINER Sven Dowideit …   0B
b9e15a5d1e1a        4 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           4 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           4 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$…   2.76kB
<missing>           4 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0B
<missing>           4 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           4 weeks ago         /bin/sh -c #(nop) ADD file:a83ab1826f43e88bc…   115MB
test@ubuntu:~/Workspace/ftp-server$
```  



> **docker images**  

{{% notice note %}}
docker images  
docker images 명령어는 System 에 가지고 있는 docker images 파일을 출력합니다.  
{{% /notice %}}

> docker images example  
```no-highlight  
test@ubuntu:~/Workspace/ftp-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ftp/pure-ftpd       14.04               9cae9b363b5a        25 minutes ago      401MB
ssh-server          git                 694b90275abf        About an hour ago   286MB
nginx               latest              10608766ca7a        21 hours ago        212MB
<none>              <none>              05b750504fe5        2 days ago          401MB
ssh-server          latest              212d3f646e97        2 days ago          211MB
httpd               latest              dabb52744997        10 days ago         178MB
ubuntu              16.04               b9e15a5d1e1a        4 weeks ago         115MB
ubuntu              14.04               c32fae490809        4 weeks ago         188MB
ubuntu              latest              cd6d8154f1e1        4 weeks ago         84.1MB
debian              latest              f2aae6ff5d89        4 weeks ago         101MB
test@ubuntu:~/Workspace/ftp-server$
```  

> **docker info**  

{{% notice note %}}
docker info  
docker info 는 docker 정보를 출력합니다.  
{{% /notice %}}

> docker info example  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 1
Server Version: 18.06.1-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 468a545b9edcd5932818eb9de8e72413e616e86e
runc version: 69663f0bd4b60df09991c08812a60108003fa340
init version: fec3683
Security Options:
 apparmor
 seccomp
  Profile: default
Kernel Version: 4.15.0-34-generic
Operating System: Ubuntu 18.04.1 LTS
OSType: linux
Architecture: x86_64
CPUs: 2
Total Memory: 3.83GiB
Name: ubuntu
ID: JRYG:BHQ3:H35K:4BQA:4IO7:2HG4:SZ2X:34NQ:2D7P:AHBC:TIDI:BNQQ
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false

WARNING: No swap limit support
test@ubuntu:~/Workspace/ftp-server$
```

> **docker inspect**  

{{% notice note %}}
docker inspect  
docker inspect 는 docker image 정보 확인시 사용합니다.  
{{% /notice %}}


> docker inspect example  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker inspect ssh-server
[
    {
        "Id": "sha256:e18ecfd3f50a110175be67d5426acf1177ed79c25991d76da3fc04b00ee59e27",
        "RepoTags": [
            "ssh-server:latest"
        ],
        "RepoDigests": [],
        "Parent": "",
        "Comment": "Imported from -",
        "Created": "2018-10-06T07:49:25.398220135Z",
        "Container": "",
        "ContainerConfig": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": null,
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "DockerVersion": "18.06.1-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": null,
            "Cmd": null,
            "Image": "",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 180972435,
        "VirtualSize": 180972435,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/304718b9632fc8c6d7dc30ac15c3e0338ae6085522e38c1e77dc7ee78902bda7/merged",
                "UpperDir": "/var/lib/docker/overlay2/304718b9632fc8c6d7dc30ac15c3e0338ae6085522e38c1e77dc7ee78902bda7/diff",
                "WorkDir": "/var/lib/docker/overlay2/304718b9632fc8c6d7dc30ac15c3e0338ae6085522e38c1e77dc7ee78902bda7/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:98dd9e2ccb30156580d099fd296c8ee19fb34fcb5ae192b82bd5d4b42c8e74a7"
            ]
        },
        "Metadata": {
            "LastTagTime": "2018-10-06T16:49:25.417762839+09:00"
        }
    }
]
test@ubuntu:~/Workspace/ftp-server$
```


> **docker kill**  

{{% notice note %}}
docker kill  
docker kill 명령어는 컨테이너에 kill 시그널을 보낼때 사용합니다.  
{{% /notice %}}


> **docker kill example**  
> docker kill 명령어로 컨테이너에 kill 시그널을 보낼수 있습니다.  
> docker kill $컨테이너 $컨테이너 형식으로 사용합니다.  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
74c9222eed72        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   5 seconds ago       Up 2 seconds        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
beb0f4dd649d        ssh-server            "/usr/sbin/sshd -D"      5 seconds ago       Up 4 seconds        0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker kill ssh-server
ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
74c9222eed72        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   10 seconds ago      Up 7 seconds        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$
```

> **docker save**  

{{% notice note %}}
docker save  
docker save 는 컨테이너를 tar 파일로 만들때 사용합니다.  
docker save 로 만든 파일은 docker load 로 image 를 만들수 있습니다.  
{{% /notice %}}

```no-highlight
docker image 를 확인 합니다. 

test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
78afd709f97f        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   50 seconds ago      Up 46 seconds       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
ad1316efdad9        ssh-server            "/usr/sbin/sshd -D"      50 seconds ago      Up 48 seconds       0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$

docker save 명령어로 iamges 파일을 tar 파일로 압축 합니다. 
test@ubuntu:~/Workspace/ftp-server$ docker save -o ssh-server.tar ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker save -o ftpd.tar ftp/pure-ftpd:14.04
test@ubuntu:~/Workspace/ftp-server$ ls
docker-compose.yml  ftpd.tar  pure-ftpd  ssh-server  ssh-server.tar
test@ubuntu:~/Workspace/ftp-server$
```


> **docker load**


{{% notice note %}}
docker load 는 tar 파일에서 image 로 load 할때 사용합니다.  
docker Version 에 따라 상이 하지만 load 는 일반적으로 Docker save 로 image 를 만들때 사용합니다.  
docker export 로 만든 tar 파일의 경우 docker load 로 image 가 안만들어질수 있습니다.  
{{% /notice %}}

> **docker load example**  
> 모든 컨테이너 이미지를 삭제 합니다.  
> docker save 로 tar 파일로 만든 이미지를 load 합니다.  
> docker-compose up -d 로 컨테이너를 구동하고 docker ps 로 컨테이너 작동상태를 확인 합니다.  

{{% notice warning %}}
docker load example  
test 내역중 docker rmi -f  명령어는 모든 image를 삭제하는 명령어 입니다.  
사용시 주의가 필요합니다.  
{{% /notice %}}



```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker rmi -f `docker images`
test@ubuntu:~/Workspace/ftp-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test@ubuntu:~/Workspace/ftp-server$
test@ubuntu:~/Workspace/ftp-server$ docker load -i ssh-server.tar
8823818c4748: Loading layer [==================================================>]    119MB/119MB
19d043c86cbc: Loading layer [==================================================>]  15.87kB/15.87kB
883eafdbe580: Loading layer [==================================================>]  14.85kB/14.85kB
4775b2f378bb: Loading layer [==================================================>]  5.632kB/5.632kB
75b79e19929c: Loading layer [==================================================>]  3.072kB/3.072kB
0e2fd56bd9bc: Loading layer [==================================================>]  100.3MB/100.3MB
4b6dd9f5e3bd: Loading layer [==================================================>]  3.072kB/3.072kB
Loaded image: ssh-server:latest
test@ubuntu:~/Workspace/ftp-server$ docker load -i ftpd.tar
aa4e7d478f39: Loading layer [==================================================>]  196.9MB/196.9MB
6d734414ae85: Loading layer [==================================================>]  209.9kB/209.9kB
52221a15d8f8: Loading layer [==================================================>]  7.168kB/7.168kB
616fe7ac49b1: Loading layer [==================================================>]  5.632kB/5.632kB
1ff19b5310ed: Loading layer [==================================================>]  3.072kB/3.072kB
88f620d0c1bf: Loading layer [==================================================>]  196.9MB/196.9MB
72bcad72424d: Loading layer [==================================================>]  13.94MB/13.94MB
2bc539ce1341: Loading layer [==================================================>]  5.565MB/5.565MB
7afabf3a346a: Loading layer [==================================================>]  10.75kB/10.75kB
6eb26301326c: Loading layer [==================================================>]  4.224MB/4.224MB
621d3211a1a4: Loading layer [==================================================>]  580.6kB/580.6kB
c9c6c8bd9cf4: Loading layer [==================================================>]  368.1kB/368.1kB
fe902615d94d: Loading layer [==================================================>]  3.072kB/3.072kB
442305c8d227: Loading layer [==================================================>]   5.12kB/5.12kB
d78b80eb41d2: Loading layer [==================================================>]  3.072kB/3.072kB
Loaded image: ftp/pure-ftpd:14.04
test@ubuntu:~/Workspace/ftp-server$
test@ubuntu:~/Workspace/ftp-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ftp/pure-ftpd       14.04               e1194eb48168        8 minutes ago       401MB
ssh-server          latest              991f4ca29796        13 minutes ago      211MB
test@ubuntu:~/Workspace/ftp-server$ docker-compose up -d
Creating ftpd ... done
Creating ftpd ...
test@ubuntu:~/Workspace/ftp-server$
```

> **docker pause**

{{% notice note %}}
docker pause  
docker pause 명령어는 컨테이너 내의 모든 프로세스를 일시 정지 합니다.  
{{% /notice %}}


> **docker pause example**  
> docker pause 명령어로 ssh-server 를 일시 정지 상태로 만듭니다.  
> docker ps 명령어로 ssh-server STATUS 를 확인 합니다.  

```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      4 minutes ago       Up 4 minutes        0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   4 minutes ago       Up 4 minutes        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$ docker pause ssh-server
ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                  PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      4 minutes ago       Up 4 minutes (Paused)   0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   4 minutes ago       Up 4 minutes            0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$
```

> **docker unpause**  

{{% notice note %}}
docker unpause  
docker unpause 명령어는 컨테이너의 일시 정지 상태에서 되돌아 올때 사용합니다.  
{{% /notice %}}

> docker unpause example
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker unpause ssh-server
ssh-server
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      11 minutes ago      Up 11 minutes       0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   11 minutes ago      Up 11 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$
```


> **docker port**  

{{% notice note %}}
docker port  
docker port 명령어는 특정 컨테이너 port 연결정보를 확인할때 사용합니다.  
{{% /notice %}}

> docker port example
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker port ssh-server
22/tcp -> 0.0.0.0:12341
test@ubuntu:~/Workspace/ftp-server$

```

> **docker ps**  

{{% notice note %}}
docker ps  
docker ps 명령어는 작동중인 컨테이너 확인시 사용합니다.  
docker ps -a 옵션으로 정지중인 컨테이너 정보를 확인 할수 있습니다.  
{{% /notice %}}


> **docker ps example**  
> docker ps 명령어로 작동중인 컨테이너를 확인 합니다.  
> docker ps -a 옵션을 사용하여 정지중인 컨테이너를 확인 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   15 minutes ago      Up 14 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$ docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                     PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      15 minutes ago      Exited (0) 4 seconds ago                                                              ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   15 minutes ago      Up 15 minutes              0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$
```

> **docker search**  

{{% notice note %}}
docker search  
docker search 명령어는 docker 이미지 저장소에서 images 검색시 사용합니다.  
{{% /notice %}}

> **docker search example**  
> docker search 명령어를 이용하여 Apache 이미지를 검색합니다.  
```no-highlight
test@ubuntu:~/Workspace$ docker search httpd
NAME                                    DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
httpd                                   The Apache HTTP Server Project                  2039                [OK]
hypriot/rpi-busybox-httpd               Raspberry Pi compatible Docker Image with a …   44
centos/httpd                                                                            21                                      [OK]
centos/httpd-24-centos7                 Platform for running Apache httpd 2.4 or bui…   16
armhf/httpd                             The Apache HTTP Server Project                  8
macadmins/netboot-httpd                 use in combination with bruienne/bsdpy          6                                       [OK]
tplatform/aws-linux-httpd24-php70       aws-linux-httpd24-php70                         2                                       [OK]
salim1983hoop/httpd24                   Dockerfile running apache config                2                                       [OK]
tplatform/aws-linux-httpd24-php71-fpm   aws-linux-httpd24-php71-fpm                     1                                       [OK]
tplatform/aws-linux-httpd24-php71       aws-linux-httpd24-php71                         1                                       [OK]
epflidevelop/os-wp-httpd                WP httpd                                        1                                       [OK]
lead4good/httpd-fpm                     httpd server which connects via fcgi proxy h…   1                                       [OK]
trollin/httpd                                                                           0
manasip/httpd                                                                           0
itsziget/httpd24                        Extended HTTPD Docker image based on the off…   0                                       [OK]
dockerpinata/httpd                                                                      0
tplatform/aws-linux-2-httpd24-php72     aws-linux-2-httpd24-php72                       0                                       [OK]
interlutions/httpd                      httpd docker image with debian-based config …   0                                       [OK]
manageiq/httpd_configmap_generator      Httpd Configmap Generator                       0                                       [OK]
publici/httpd                           httpd:latest                                    0                                       [OK]
mprahl/s2i-angular-httpd24              An S2I image for building and running Angula…   0                                       [OK]
buzzardev/httpd                         Based on the official httpd image               0                                       [OK]
amd64/httpd                             The Apache HTTP Server Project                  0
manageiq/httpd                          Container with httpd, built on CentOS for Ma…   0                                       [OK]
cilium/demo-httpd                                                                       0
test@ubuntu:~/Workspace$
```


> **docker pull**  

{{% notice note %}}
docker pull  
docker pull 명령어는 docker image 저장소에서 image 를 Down 할때 사용합니다.  
{{% /notice %}}

> **docker pull example**  
> Apache 마지막 버젼 이미지를 다운로드 합니다.  
```no-highlight
test@ubuntu:~/Workspace$ docker pull httpd:latest
latest: Pulling from library/httpd
f189db1b88b3: Pull complete
ba2d31d4e2e7: Pull complete
23a65f5e3746: Pull complete
5e8eccbd4bc6: Pull complete
4c145eec18d8: Pull complete
c64fd93430a0: Pull complete
c9efc31823a2: Pull complete
Digest: sha256:81bc5f68f994a3c7bffc5d6ecba9e4fde70488c43ee8d57846a45c4995c67a23
Status: Downloaded newer image for httpd:latest
test@ubuntu:~/Workspace$
```


> **docker rename**  

{{% notice note %}}
docker rename  
docker rename 은 컨테이너 이름 변경시 사용합니다.  
{{% /notice %}}

> **docker rename example**  
> docker ps 로 작동중인 컨테이너를 확인 합니다.  
> docker rename 명령어로 기존 ftpd 컨테이너의 이름에서 pure-ftpd 로 변경합니다.  
```no-highlight
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   24 minutes ago      Up 24 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace$ docker rename ftpd pure-fptd
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   25 minutes ago      Up 25 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$
```

> **docker start**  

{{% notice note %}}
docker start  
docker start 명령어는 컨테이너 실행시 사용됩니다.  
{{% /notice %}}

> docker start example  

```no-highlight
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   28 minutes ago      Up 28 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ftp/pure-ftpd       14.04               e1194eb48168        36 minutes ago      401MB
ssh-server          latest              991f4ca29796        41 minutes ago      211MB
httpd               latest              dabb52744997        10 days ago         178MB
test@ubuntu:~/Workspace$ docker start ssh-server
ssh-server
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      28 minutes ago      Up 2 seconds        0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   28 minutes ago      Up 28 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$
```

> **docker stats**  

{{% notice note %}}
docker stats  
docker status 명령어는 컨테이너 자원 사용량을 모니터링 할때 사용합니다.  
{{% /notice %}}

> docker stats example  

```no-highlight
test@ubuntu:~/Workspace$ docker stats ssh-server
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT    MEM %               NET I/O             BLOCK I/O           PIDS
8505aabf31e1        ssh-server          0.00%               1.852MiB / 3.83GiB   0.05%               796B / 0B           0B / 0B             1
```


> **docker stop**  

{{% notice note %}}
docker stop  
docker stop 명령어는 컨테이너를 정지 할때 사용합니다.  
{{% /notice %}}


> docker stop example  
```no-highlight
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      32 minutes ago      Up 4 minutes        0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   32 minutes ago      Up 32 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$ docker stop ssh-server
ssh-server
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   32 minutes ago      Up 32 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$

```

> **docker restart**  

{{% notice note %}}
docker restart  
docker restart 명령어는 컨테이너 재시작시 사용합니다.  
{{% /notice %}}


> docker restart example  

```no-highlight
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   32 minutes ago      Up 32 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$ docker restart ssh-server
ssh-server
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      33 minutes ago      Up 1 second         0.0.0.0:12341->22/tcp                                      ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   33 minutes ago      Up 33 minutes       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$
```

> **docker rm**

{{% notice note %}}
docker rm  
docker rm 명령어는 컨테이너를 삭제 할때 사용합니다.  
{{% /notice %}}

> **docker rm example**  
> docker rm 명령어로 컨테이너를 삭제 합니다.  

```no-highlight
test@ubuntu:~/Workspace$ docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                     PORTS                                                      NAMES
8505aabf31e1        ssh-server            "/usr/sbin/sshd -D"      3 hours ago         Exited (0) 6 seconds ago                                                              ssh-server
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   3 hours ago         Up 3 hours                 0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$ docker rm ssh-server
ssh-server
test@ubuntu:~/Workspace$ docker ps -a
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
f6ead849c5db        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   3 hours ago         Up 3 hours          0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   pure-fptd
test@ubuntu:~/Workspace$
```

> **docker rmi**  

{{% notice note %}}
docker rmi  
docker rmi 명령어는 도커 이미지를 삭제 합니다.  
{{% /notice %}}


> **docker rmi example**  
> docker rmi 명령어를 이용하여 ssh-server image를 삭제 합니다.  
> docker rmi 명령어 사용시 imageID 를 사용하여 삭제합니다.  
```no-highlight

test@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ftp/pure-ftpd       14.04               e1194eb48168        3 hours ago         401MB
ssh-server          latest              991f4ca29796        3 hours ago         211MB
httpd               latest              dabb52744997        10 days ago         178MB
test@ubuntu:~/Workspace$ docker rmi 991f4ca29796
Untagged: ssh-server:latest
Deleted: sha256:991f4ca29796aa76fe238ebdf1eb00274f067522ee7a1821f04eb51f50f2d6e3
Deleted: sha256:a6753658624e2a0ac74366be1ee3538d04cc7322e28142e688967b0262c8e96c
Deleted: sha256:40d8eba6ec133c7a8c7f6383efdf27fcf64c60e15cb3f4d51cb7a7ffa37c5f34
Deleted: sha256:479fa3c649ef40ba88c3ff090b963dca3189ad1b77d9d734f8cd4d9079ea3990
Deleted: sha256:bd78a203e965358e5e95b20c5fa4b3e3599d1cbc58d00522324e233e7cbf8554
Deleted: sha256:f5aad241af819c313bd6f5fbfeaae096f0fc977c0806fc9b1464d2260adacd22
Deleted: sha256:7c82a79e7c32df5cd4d9f9ec8da86396c06e6dcfa99d5e991c2e98b8e804e8d0
Deleted: sha256:8823818c474862932702f8a920abea43b2560ddceb910d145be9ba0eb149a643
test@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ftp/pure-ftpd       14.04               e1194eb48168        3 hours ago         401MB
httpd               latest              dabb52744997        10 days ago         178MB
test@ubuntu:~/Workspace$
```

> **docker run**  

{{% notice note %}}
docker run  
docker run 명령어는 컨테이너 실행시 사용합니다.  
docker run 명령어는 image 파일을 pull / start 까지 하여 컨테이너를 구동상태로 만듭니다.  
{{% /notice %}}

> **docker run example**  
> docker run 으로 Apache 를 구동 합니다.  
> --name 옵션을 사용하여 컨테이너 이름을 web-service 로 생성 합니다.  
```no-highlight
test@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
test@ubuntu:~/Workspace$ docker run --name web-service -d -p 80:80 httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
f189db1b88b3: Pull complete
ba2d31d4e2e7: Pull complete
23a65f5e3746: Pull complete
5e8eccbd4bc6: Pull complete
4c145eec18d8: Pull complete
c64fd93430a0: Pull complete
c9efc31823a2: Pull complete
Digest: sha256:81bc5f68f994a3c7bffc5d6ecba9e4fde70488c43ee8d57846a45c4995c67a23
Status: Downloaded newer image for httpd:latest
cb3598dfd4df44c61ee0191a017030a30b7f462ab1cc7de3b7e9d1b82230f2aa
test@ubuntu:~/Workspace$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
cb3598dfd4df        httpd               "httpd-foreground"   4 seconds ago       Up 2 seconds        0.0.0.0:80->80/tcp   web-service
test@ubuntu:~/Workspace$
```

> **docker tag**

{{% notice note %}}
docker tag  
docker tag 명령어는 source image 가 참조하는 target images 태그를 만듭니다.  
{{% /notice %}}

> **docker tag example**  
> docker images 명령어로 이미지를 검색 합니다.  
> docker tag 를 이용하여 기존 httpd 이미지에 web-test 태그를 만듭니다.  
> 이미지를 확인 합니다.  
```no-highlight
est@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              dabb52744997        10 days ago         178MB
test@ubuntu:~/Workspace$ docker tag dabb52744997 web-test
test@ubuntu:~/Workspace$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              dabb52744997        10 days ago         178MB
web-test            latest              dabb52744997        10 days ago         178MB
test@ubuntu:~/Workspace$
```

> **docker top**  

{{% notice note %}}
docker top  
docker top 명령어는 컨테이너 에서 실행중인 프로세스를 표시합니다.  
{{% /notice %}}


> **docker top example**    
> docker top 명령어로 web-service 에서 사용하는 프로세스를 확인 합니다.  

```no-highlight
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
cb3598dfd4df        httpd               "httpd-foreground"   6 minutes ago       Up 6 minutes        0.0.0.0:80->80/tcp   web-service
test@ubuntu:~$ docker top web-service
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                39246               39225               0                   20:04               ?                   00:00:00            httpd -DFOREGROUND
daemon              39305               39246               0                   20:04               ?                   00:00:00            httpd -DFOREGROUND
daemon              39306               39246               0                   20:04               ?                   00:00:00            httpd -DFOREGROUND
daemon              39307               39246               0                   20:04               ?                   00:00:00            httpd -DFOREGROUND
test@ubuntu:~$

```


> **docker update**  

{{% notice note %}}
docker update  
docker update 명령어는 하나이상의 컨테이너 구성을 업데이트 합니다.  
{{% /notice %}}


> **docker update example**  
> docker update 명령어를 사용하여 컨테이너가 System 리부팅시에도 자동으로 시작될수 있도록 설정 합니다.  
> --restart=always 옵션을 사용합니다.  
> --restart=no 옵션은 always 의 반대 옵션입니다.  
```no-highlight

test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
cb3598dfd4df        httpd               "httpd-foreground"   12 minutes ago      Up 12 minutes       0.0.0.0:80->80/tcp   web-service
test@ubuntu:~/Workspace/ftp-server$ docker update --restart=always web-service
web-service
test@ubuntu:~/Workspace/ftp-server$
```

> **docker version**  

{{% notice note %}}
docker version  
docker version 명령어는 docker version 정보를 출력합니다.  
{{% /notice %}}

> **docker version example**  
> docker version 을 확인 합니다.  

```no-highlight
test@ubuntu:~/Workspace$ docker version
Client:
 Version:           18.06.1-ce
 API version:       1.38
 Go version:        go1.10.3
 Git commit:        e68fc7a
 Built:             Tue Aug 21 17:24:51 2018
 OS/Arch:           linux/amd64
 Experimental:      false

Server:
 Engine:
  Version:          18.06.1-ce
  API version:      1.38 (minimum version 1.12)
  Go version:       go1.10.3
  Git commit:       e68fc7a
  Built:            Tue Aug 21 17:23:15 2018
  OS/Arch:          linux/amd64
  Experimental:     false
test@ubuntu:~/Workspace$
```


<br></br>  