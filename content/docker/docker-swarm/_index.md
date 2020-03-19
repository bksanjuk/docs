+++
title = "Docker Swarm"
weight = 10
+++


## Docker Swarm  

![docker15](/docker/docker_tmp/D15.png)  

{{% notice note %}}
**Docker Swarm 소개**  
Docker Swarm 로 컨테이너를 클러스터링 할수 있습니다.  
소규모의 컨테이너 운영시 kubernetes 보다 간편하게 컨테이너 클러스터를 구성 할수 있습니다.  
{{% /notice %}}


> **VM 준비**  
> Swarm 에서 사용할 모든 vm 에 docker 가 설치 되어 있습니다.  
> docker-machine 을 이용하지 않은 방법을 소개 하고 있습니다.  
> vm 준비시 docker-machine 을 이용한다면 조금더 편하게 vm 을 생성 할수 있습니다.  


|hostname   	|ip   	|
|---	|---	|
|docker-master   	|192.168.0.10   	|
|docker-worker01   	|192.168.0.20   	|
|docker-worker02   	|192.168.0.30   	|


> **Docker 설치 확인**  
```no-highlight
test@docker-master:~$ docker -v
Docker version 18.06.1-ce, build e68fc7a

test@docker-worker01:~$ docker -v
Docker version 18.06.1-ce, build e68fc7a

test@docker-worker02:~$ docker -v
Docker version 18.06.1-ce, build e68fc7a

```

> **Create Docker Swarm Cluster**  
> docker-master host 에서 작업 (Docker Swarm Cluster 를 생성합니다. )  
> Command : docker swarm init - advertise-addr $docker-master ip  
```no-highlight
test@docker-master:~$ docker swarm init --advertise-addr 192.168.0.10
Swarm initialized: current node (pbaa14jv3f351lmufm0rwcixw) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1u5vl675mm5jy794e9cjv05z08ff132jm0vwzie7sh6kz26dwo-6d6uj014xwu11k7px64rwenkb 192.168.0.10:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

test@docker-master:~$

```

{{% notice note %}}
**docker swarm leave 명령어**  
docker swarm 구성이 잘못된 경우 docker swarm leave 명령어로 docker swarm 구성에서 제외 할수 있습니다.  
docker swarm leave 명령어 사용시 master node 이외 worker node 에서도 작업이 필요 합니다.  
{{% /notice %}}

> **docker swarm leave**
```no-highlight
test@docker-master:~$ docker swarm leave -f
Node left the swarm.
test@docker-master:~$ docker info |grep -i swarm
WARNING: No swap limit support
Swarm: inactive
test@docker-master:~$
```


> **docker node 확인**  
docker-master 에서 생성된 docker node 를 확인 합니다.  
```no-highlight
test@docker-master:~$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
pbaa14jv3f351lmufm0rwcixw *   docker-master       Ready               Active              Leader              18.06.1-ce
test@docker-master:~$
test@docker-master:~$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
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
Swarm: active
 NodeID: pbaa14jv3f351lmufm0rwcixw
 Is Manager: true
 ClusterID: ryg16z7790io5i5cp7lyig6xk
 Managers: 1
 Nodes: 1
 Orchestration:
  Task History Retention Limit: 5
 Raft:
  Snapshot Interval: 10000
  Number of Old Snapshots to Retain: 0
  Heartbeat Tick: 1
  Election Tick: 10
 Dispatcher:
  Heartbeat Period: 5 seconds
 CA Configuration:
  Expiry Duration: 3 months
  Force Rotate: 0
 Autolock Managers: false
 Root Rotation In Progress: false
 Node Address: 192.168.0.10
 Manager Addresses:
  192.168.0.10:2377
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
Kernel Version: 4.4.0-62-generic
Operating System: Ubuntu 16.04.2 LTS
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.936GiB
Name: docker-master
ID: DI4N:3HNO:74TW:4AHK:SO2J:L3EA:HAKL:YQ3G:23LB:NOAL:5PMN:TIFM
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
test@docker-master:~$

```


> **join-token 생성**  
> docker-master host 에서 join-token 을 생성 합니다.  
```no-highlight
test@docker-master:~$ docker swarm join-token worker
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1u5vl675mm5jy794e9cjv05z08ff132jm0vwzie7sh6kz26dwo-6d6uj014xwu11k7px64rwenkb 192.168.0.10:2377

test@docker-master:~$

```

> **docker-worker Swarm 등록**  
> docker-master host 에서 생성한 join-token 을 이용하여 worker node 를 클러스터에 등록 합니다.  
> docker-worker01 host 에서 Swarm Cluster 에 등록하고 Swarm 상태를 확인 합니다.  
```no-highlight
test@docker-worker01:~$ docker swarm join --token SWMTKN-1-1u5vl675mm5jy794e9cjv05z08ff132jm0vwzie7sh6kz26dwo-6d6uj014xwu11k7px64rwenkb 192.168.0.10:2377
This node joined a swarm as a worker.
test@docker-worker01:~$ docker info|grep -i swarm
Swarm: active
WARNING: No swap limit support
test@docker-worker01:~$
```


> docker-worker02 host 에서 Swarm Cluster 에 등록하고 Swarm 상태를 확인 합니다.  
```no-highlight
test@docker-worker02:~$ docker swarm join --token SWMTKN-1-1u5vl675mm5jy794e9cjv05z08ff132jm0vwzie7sh6kz26dwo-6d6uj014xwu11k7px64rwenkb 192.168.0.10:2377
This node joined a swarm as a worker.
test@docker-worker02:~$ docker info |grep -i swarm
WARNING: No swap limit support
Swarm: active
test@docker-worker02:~$
```


> **Swarm node 확인**  
> docker-master 에서 Swarm node 를 확인 합니다.  
```no-highlight
test@docker-master:~$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
pbaa14jv3f351lmufm0rwcixw *   docker-master       Ready               Active              Leader              18.06.1-ce
uac58mks3ag22eiru1qwg3e3g     docker-worker01     Ready               Active                                  18.06.1-ce
emxyua7vz31do60n5dyo96j0y     docker-worker02     Ready               Active                                  18.06.1-ce
test@docker-master:~$
```


> **Nginx 컨테이너 테스트**  

{{% notice note %}}
**docker service create 명령어**  
docker swarm 의 경우 docker run 대신 docker service create 명령어를 사용합니다.  
Swarm nginx test 문서의 경우 바로 적용하여 사용하는 문서가 아니며 service 생성  
Swarm 기본 적인 명령어 위주로 작성 하였습니다.  
{{% /notice %}}


> **docker service create**  
> docker-master host 에서 docker service create 명령어를 이용하여 web-service 컨테이너를 생성합니다.  

```no-highlight
test@docker-master:~$ docker service create --name web-service --publish 80:80 nginx
md8pzk0owb4s8ida55voqzlrb
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged
test@docker-master:~$ 
```

> **docker service 확인**  

```no-highlight

test@docker-master:~$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
md8pzk0owb4s        web-service         replicated          1/1                 nginx:latest        *:80->80/tcp
test@docker-master:~$ docker service ps web-service
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
ksugzzmulbh1        web-service.1       nginx:latest        docker-master       Running             Running about a minute ago
test@docker-master:~$


```

> **docker service scale**  
> docker service scale 명령어를 사용하여 web-service 컨테이너를 추가 할수 있습니다.  
> 컨테이너 1 -> 3대로 추가  
```no-highlight
test@docker-master:~$ docker service scale web-service=3
web-service scaled to 3
overall progress: 3 out of 3 tasks
1/3: running   [==================================================>]
2/3: running   [==================================================>]
3/3: running   [==================================================>]
verify: Service converged
test@docker-master:~$ docker service ps web-service
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
ksugzzmulbh1        web-service.1       nginx:latest        docker-master       Running             Running 7 minutes ago
umfp8ntznr2r        web-service.2       nginx:latest        docker-worker02     Running             Running 10 seconds ago
1yuz9z9xxpws        web-service.3       nginx:latest        docker-worker01     Running             Running 20 seconds ago
test@docker-master:~$
```

> **컨테이너 Service 3대 -> 5대 추가**  
```no-highlight
test@docker-master:~$ docker service scale web-service=5
web-service scaled to 5
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
test@docker-master:~$ docker service ps web-service
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
ksugzzmulbh1        web-service.1       nginx:latest        docker-master       Running             Running 8 minutes ago
umfp8ntznr2r        web-service.2       nginx:latest        docker-worker02     Running             Running about a minute ago
1yuz9z9xxpws        web-service.3       nginx:latest        docker-worker01     Running             Running about a minute ago
m3op5xxy012p        web-service.4       nginx:latest        docker-master       Running             Running 44 seconds ago
yz1me2r5cbdv        web-service.5       nginx:latest        docker-worker02     Running             Running 44 seconds ago
test@docker-master:~$
```


> **컨테이너 Service 5대 -> 1대**  
컨테이너 서비스의 증가뿐 아니라 scale out 시에도 사용됩니다.  

```no-highlight
test@docker-master:~$ docker service scale web-service=1
web-service scaled to 1
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged
test@docker-master:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
3bee481b66c5        nginx:latest        "nginx -g 'daemon of…"   9 minutes ago       Up 9 minutes        80/tcp              web-service.1.ksugzzmulbh1r0gd5ztm38v8u
test@docker-master:~$
```


> **docker service rm**  
> swarm 에서 사용중인 컨테이너 삭제시 사용됩니다.  
```no-highlight
test@docker-master:~$ docker service rm web-service
web-service
test@docker-master:~$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
test@docker-master:~$
```

<br></br>  