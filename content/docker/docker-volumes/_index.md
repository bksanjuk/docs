+++
title = "Docker Volumes"
weight = 8
+++



## Docker Volumes

> docker 에서는 3가지 유형으로 데이터를 관리 할수 있습니다.  

![docker16](/docker/docker_tmp/D16.png)  

|mount 방식    	|설명    	|비고    	|
|---	|---	|---	|
|docker volume    	|docker 컨테이너에 의해 생성되고 관리 됩니다.      	|    	|
|bind mount    	|호스트 시스템의 파일 또는 디렉토리가 컨테이너 볼륨과 연결되어 사용됩니다.      	|    	|
|tmpfs mount   	|일시적으로 사용할수 있고 메모리에만 지속되며, 컨테이너가 중지되면 tmpfs 마운트가 제저 됩니다.      	|   	|


>  **tmpfs mount 방식의 경우 data 유지가 불가능 합니다.**  


>* docker volume 명령어  
```no-highlight
test@docker-test:~/Workspace/test$ docker volume

Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes

Run 'docker volume COMMAND --help' for more information on a command.
test@docker-test:~/Workspace/test$

```



> **docker volume create**  
> vol01 을 만들고 만들어진 볼륨을 확인 합니다.  
> docker volume inspect 명령어로 vol01 의 볼륨 정보를 확인 합니다.  
```no-highlight
test@docker-test:~$ docker volume create vol01
vol01
test@docker-test:~$ docker volume ls
DRIVER              VOLUME NAME
local               vol01
test@docker-test:~$ docker volume inspect vol01
[
    {
        "CreatedAt": "2018-10-13T21:43:47+09:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/vol01/_data",
        "Name": "vol01",
        "Options": {},
        "Scope": "local"
    }
]
test@docker-test:~$
```

> **docker volume rm 명령어**  
> vol01 을 삭제 합니다.  
```no-highlight
test@docker-test:~/Workspace/test$ docker volume rm vol01
vol01
test@docker-test:~/Workspace/test$ docker volume ls
DRIVER              VOLUME NAME
test@docker-test:~/Workspace/test$
```

> docker volume 을 이용하여 컨테이너를 실행 합니다.  

{{% notice note %}}
vol02 volume 을 생성 합니다.  
mount source 를 vol02 로 지정 하여 nginx 컨테이너를 실행 합니다.  
컨테이너 내부에서 touch 를 이용하여 빈 파일을 생성 합니다.  
nginx-test mount 정보는 docker inspect 명령어로 확인 할수 있습니다.
{{% /notice %}}

```no-highlight
test@docker-test:~/Workspace/test$ docker volume create vol02
vol02
test@docker-test:~/Workspace/test$ docker run -d --name test-nginx --mount source=vol02,target=/app nginx:latest

test@docker-test:~/Workspace/test$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
514ac031e078        nginx:latest        "nginx -g 'daemon of…"   11 seconds ago      Up 10 seconds       80/tcp              test-nginx
test@docker-test:~/Workspace/test$ docker exec -it 514ac031e078 /bin/bash
root@514ac031e078:/# cd /app
root@514ac031e078:/app# touch 0
root@514ac031e078:/app# exit



test@docker-test:~/Workspace/test$ sudo ls -al /var/lib/docker/volumes/vol02/_data
total 8
drwxr-xr-x 2 root root 4096 Oct 13 21:52 .
drwxr-xr-x 3 root root 4096 Oct 13 21:50 ..
-rw-r--r-- 1 root root    0 Oct 13 21:52 0
test@docker-test:~/Workspace/test$


nginx-test 컨테이너의 mount 정보를 확인 합니다. 
test@docker-test:~/Workspace/test$ docker inspect nginx-test


      "Mounts": [
            {
                "Type": "volume",
                "Name": "vol02",
                "Source": "/var/lib/docker/volumes/vol02/_data",
                "Destination": "/app",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
```

> **Dockerfile 에서 Volume 사용**  
> Dockerfile 에서 Volume 사용시 아래와 같이 설정해 주면 됩니다.  
> 기존에 생성이 되었던 Volume 사용시에도 동일하게 사용되며, 볼륨이 없으면 자동으로 Volume 을 생성합니다.  
```no-highlight
test@docker-test:~/Workspace/nginx$ vi Dockerfile
FROM ubuntu:16.04
MAINTAINER user01 <test@anonymouse.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx


VOLUME web01

WORKDIR /etc/nginx

CMD ["nginx"]

EXPOSE 80
EXPOSE 443

test@docker-test:~/Workspace/nginx$ docker build --tag nginx-test .

test@docker-test:~/Workspace/nginx$ docker run --name web-service -d -p 80:80 -v web01:/data nginx-test

test@docker-test:~/Workspace/nginx$ docker inspect web-service
        "Mounts": [
            {
                "Type": "volume",
                "Name": "web01",
                "Source": "/var/lib/docker/volumes/web01/_data",
                "Destination": "/data",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "b48d08c5d03684301a510f61b16bb060e4d24736827ed4a15f008b95ce81b08f",
                "Source": "/var/lib/docker/volumes/b48d08c5d03684301a510f61b16bb060e4d24736827ed4a15f008b95ce81b08f/_data",
                "Destination": "web01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
```

{{% notice tip %}}
volume 연결시 해당 컨테이너와 볼륨을 연결 하기 위한 volume 이 자동으로 생성 됩니다.  
hash 값으로 으로 생성되어 아래와 같이 확인 할수 있습니다.  
{{% /notice %}}

```no-highlight
test@docker-test:~/Workspace/nginx$ docker run --name web-service -d -p 80:80 -v /home/test/Workspace/nginx/data:/data nginx-test

test@docker-test:~/Workspace/nginx$ docker volume ls
DRIVER              VOLUME NAME
local               e8e639ad6a1cde2d17d408fe0ff9b648e5a81f7c72f3f4b452693c6640f052a4
test@docker-test:~/Workspace/nginx$
```

{{% notice note %}}
docker-compose mariadb example  
mariadb 데이터 용으로 사용할 docker volume 을 지정 합니다.  
docker-compose.yml 파일을 작성 합니다.
{{% /notice %}}

```no-highlight
dbvol01 이 아닌 test_dbvol01 을 사용 합니다.  
미리 사용한 볼륨을 사용하지 않는 경우에는 docker-comopse 에 docker volume 을 지정 하여  
사용 할수 있습니다.  
test@docker-test:~/Workspace/test$ vi docker-compose.yml
version: '2'

services:
    mariadb:
        image: mariadb:10.1
        ports:
            - 3306:3306
        volumes:
            - dbvol01:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_USER: docker
            MYSQL_PASSWORD: docker
            MYSQL_DATABASE: docker
volumes:
  dbvol01:

test@docker-test:~/Workspace/test$ docker-compose up -d --build

test@docker-test:~/Workspace/test$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
dafab5cedd04        mariadb:10.1        "docker-entrypoint.s…"   45 seconds ago      Up 44 seconds       0.0.0.0:3306->3306/tcp   test_mariadb_1
test@docker-test:~/Workspace/test$ docker inspect test_mariadb_1

        "Mounts": [
            {
                "Type": "volume",
                "Name": "test_dbvol01",
                "Source": "/var/lib/docker/volumes/test_dbvol01/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "rw",
                "RW": true,
                "Propagation": ""
            }
        ],
```

{{% notice note %}}
docker-compose 에서 docker volume 사용참고  
docker-compose 에서는 docker volume 사용시 project name 을 사용합니다.  
미리 설정한 docker volume 을 사용하기 위해서는 external 옵션을 사용 해야 합니다.  
{{% /notice %}}


> **docker volume create 로 만든 volume 사용**  
> docker volume 을 docker-compose 에서 사용하기 위해서는 external true 를 사용해야 합니다.  
> external true 옵션을 사용 안하고 volume 을 지정 하게 되면 docker-project name 으로 volume 이 생성 됩니다.  

```no-highlight
test@docker-test:~/Workspace/test$ docker volume create dbvol01
dbvol01

test@docker-test:~/Workspace/test$ cat docker-compose.yml
version: '2'

services:
    mariadb:
        image: mariadb:10.1
        ports:
            - 3306:3306
        volumes:
            - dbvol01:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_USER: docker
            MYSQL_PASSWORD: docker
            MYSQL_DATABASE: docker
volumes:
    dbvol01:
      external: true



test@docker-test:~/Workspace/test$ docker-compose up -d --build

test@docker-test:~/Workspace/test$ docker inspect test_mariadb_1


        "Mounts": [
            {
                "Type": "volume",
                "Name": "dbvol01",
                "Source": "/var/lib/docker/volumes/dbvol01/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "rw",
                "RW": true,
                "Propagation": ""
            }
        ],


test@docker-test:~/Workspace/test$ sudo ls -al /var/lib/docker/volumes/dbvol01/_data
[sudo] password for test:
total 110656
drwxr-xr-x 5  999 docker     4096 Oct 13 23:04 .
drwxr-xr-x 3 root root       4096 Oct 13 23:04 ..
-rw-rw---- 1  999 docker    16384 Oct 13 23:04 aria_log.00000001
-rw-rw---- 1  999 docker       52 Oct 13 23:04 aria_log_control
drwx------ 2  999 docker     4096 Oct 13 23:04 docker
-rw-rw---- 1  999 docker 12582912 Oct 13 23:04 ibdata1
-rw-rw---- 1  999 docker 50331648 Oct 13 23:04 ib_logfile0
-rw-rw---- 1  999 docker 50331648 Oct 13 23:04 ib_logfile1
-rw-rw---- 1  999 docker        0 Oct 13 23:04 multi-master.info
drwx------ 2  999 docker     4096 Oct 13 23:04 mysql
drwx------ 2  999 docker     4096 Oct 13 23:04 performance_schema
-rw-rw---- 1  999 docker    24576 Oct 13 23:04 tc.log
test@docker-test:~/Workspace/test$
```

<br></br>