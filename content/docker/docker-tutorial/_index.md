+++
title = "Docker Tutorial"
weight = 3
+++


## Docker Tutorial


> Docker Version 확인  
```no-highlight
test@docker-test:~$ docker version
Client:
 Version:      18.05.0-ce
 API version:  1.37
 Go version:   go1.9.5
 Git commit:   f150324
 Built:        Wed May  9 22:16:25 2018
 OS/Arch:      linux/amd64
 Experimental: false
 Orchestrator: swarm

Server:
 Engine:
  Version:      18.05.0-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.5
  Git commit:   f150324
  Built:        Wed May  9 22:14:32 2018
  OS/Arch:      linux/amd64
  Experimental: false
test@docker-test:~$
```

> **Docker 실행 옵션**  
> docker run 에서 사용할수 있는 옵션을 다음과 같습니다.  
```no-highlight
 docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]  
 옵션 설명  
 -d : detached mode 흔히 말하는 백그라운드 모드  
 -p : 호스트와 컨테이너의 포트를 연결 (포워딩)  
 -v : 호스트와 컨테이너의 디렉토리를 연결 (마운트)  
 -e : 컨테이너 내에서 사용할 환경변수 설정  
 -name : 컨테이너 이름 설정  
 -rm : 프로세스 종료시 컨테이너 자동제거  
 -it : -i 와 -t 를 동시에 사용한것으로 터미널 입력을 위한 옵션  
 -link : 컨테이너 연결 [컨테이너명:별칭]  
```

> **ubuntu 16.04 컨테이너 생성**  
> docker run 으로 ubuntu:16.04 를 실행 하여도 별도의 옵션이 없으면  
> ubuntu:16.04 image 만 다운로드 받습니다.실행을 하기 위해서는 옵션을 지정해야 합니다.  
```no-highlight
test@docker-test:~$ docker run ubuntu:16.04
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
3b37166ec614: Pull complete
504facff238f: Pull complete
ebbcacd28e10: Pull complete
c7fb3351ecad: Pull complete
2e3debadcbf7: Pull complete
Digest: sha256:45ddfa61744947b0b8f7f20b8de70cbcdd441a6a0532f791fd4c09f5e491a8eb
Status: Downloaded newer image for ubuntu:16.04
test@docker-test:~$
```

> **docker ps**  
> 컨테이너가 실행중인지 확인을 할수 있습니다.  
> ps -a 옵션 사용시 정지된 컨테이너 까지 확인 합니다.  
```no-highlight
test@docker-test:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
test@docker-test:~$
```

> **docker 실행**  
> docker 실행시 -it 옵션을 사용 --rm 을 사용 합니다.  
> /bin/bash 를 커맨드 라인 마지막에 추가 합니다.  
> -it /bin/bash 사용시 컨테이너 내부로 접속 할수 있습니다.  
> exit 하여 docker ps 로 확인시 컨테이너 실행이 정지된것을 확인 할수 있습니다.  
> 간단하게 테스트 할경우 docker run 을 이용하여 컨테이너 구동을 하지만 일반적으로는  
> Dockerfile 을 만들어 컨테이너를 생성 합니다.  
```no-highlight
test@ubuntu:~$ docker run --rm -it ubuntu:16.04 /bin/bash
root@8ace718689ac:/# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.5 LTS"
root@8ace718689ac:/# exit
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
test@ubuntu:~$
```

> **Docker images**  
> [Docker Hub](https://hub.docker.com/)에서 공식 images 를 제공 합니다.  
> docker search 명령어를 통하여 images 를 검색 할수 있습니다.  
> OFFICIAL [OK] image 의 경우 공식 이미지 입니다.  apache 이미지의 경우 httpd 입니다.  

```no-highlight
test@ubuntu:~$ docker search apache
NAME                                           DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
tomcat                                         Apache Tomcat is an open source implementati…   2061                [OK]
httpd                                          The Apache HTTP Server Project                  2035                [OK]
cassandra                                      Apache Cassandra is an open-source distribut…   867                 [OK]
maven                                          Apache Maven is a software project managemen…   696                 [OK]
solr                                           Solr is the popular, blazing-fast, open sour…   586                 [OK]
zookeeper                                      Apache ZooKeeper is an open-source server wh…   483                 [OK]
eboraas/apache-php                             PHP5 on Apache (with SSL support), built on …   139                                     [OK]
eboraas/apache                                 Apache (with SSL support), built on Debian      90                                      [OK]
webdevops/php-apache-dev                       PHP with Apache for Development (eg. with xd…   78                                      [OK]
webdevops/php-apache                           Apache with PHP-FPM (based on webdevops/php)    74                                      [OK]
groovy                                         Apache Groovy is a multi-faceted language fo…   58                  [OK]
tomee                                          Apache TomEE is an all-Apache Java EE certif…   56                  [OK]
nimmis/apache-php5                             This is docker images of Ubuntu 14.04 LTS wi…   53                                      [OK]
apacheignite/ignite                            Apache Ignite In-Memory docker image.           44                                      [OK]
bitnami/apache                                 Bitnami Apache Docker Image                     42                                      [OK]
linuxserver/apache                             An Apache container, brought to you by Linux…   18
apache/nutch                                   Apache Nutch                                    15                                      [OK]
1and1internet/ubuntu-16-apache-php-7.0         ubuntu-16-apache-php-7.0                        13                                      [OK]
webdevops/apache                               Apache container                                11                                      [OK]
antage/apache2-php5                            Docker image for running Apache 2.x with PHP…   10                                      [OK]
lephare/apache                                 Apache container                                4                                       [OK]
newdeveloper/apache-php                        apache-php7.2                                   2
mastertinner/apache-directory-index-resource   A Concourse resource for the apache director…   1                                       [OK]
secoresearch/apache-varnish                    Apache+PHP+Varnish5.0                           0                                       [OK]
jelastic/apachephp                             An image of the Apache PHP application serve…   0
test@ubuntu:~$
```  

> **Docker pull**  
> docker pull 명령어의 경우 images 를 System 에 다운 로드 하는 명령어 입니다.  
> docker pull httpd:Version 으로 지정 하여 사용할수 있습니다.  

```no-highlight
test@ubuntu:~$ docker pull tomcat:latest
latest: Pulling from library/tomcat
05d1a5232b46: Pull complete
5cee356eda6b: Pull complete
89d3385f0fd3: Pull complete
65dd87f6620b: Pull complete
78a183a01190: Pull complete
1a4499c85f97: Pull complete
2c9d39b4bfc1: Pull complete
1b1cec2222c9: Pull complete
fc95b85a81f3: Pull complete
0f3868647539: Pull complete
9b6a6eddb2d9: Pull complete
8787183cb077: Pull complete
Digest: sha256:8d120de5102cc12310de741299e9bf6f39d2d674663f2ce4e6f0f181ccfeebe7
Status: Downloaded newer image for tomcat:latest
test@ubuntu:~$
```

> **Docker images**  
> System 에 있는 이미지를 검색할때 사용합니다.  
> 사용시 images -a 옵션으로 모든 이미지를 확인할수 있습니다.  
```no-highlight
test@ubuntu:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
httpd               latest              dabb52744997        6 days ago          178MB
tomcat              latest              41a54fe1f79d        2 weeks ago         463MB
mariadb             latest              199cc294e316        3 weeks ago         363MB
mysql               5.7                 563a026a1511        3 weeks ago         372MB
test@ubuntu:~$
```


> **Apache 컨테이너 실행**  
> -d 옵션을 사용 하여 컨테이너 백그라운드 실행  
> -p 외부포트:내부포트 연결  
> --name httpd-test 이름지정  
>  httpd images 이름  
```no-highlight
test@ubuntu:~$ docker run -d -p 80:80 --name httpd-test httpd
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
9e02908f90d24120f60e9b2f45887fbc528d34c50644676bdcdbe5de593733c6
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
9e02908f90d2        httpd               "httpd-foreground"   4 seconds ago       Up 2 seconds        0.0.0.0:80->80/tcp   httpd-test
test@ubuntu:~$
```

> **webpage 확인**  
> curl 명령어로 localhost 정보를 가지고 옵니다.  
```no-highlight
test@ubuntu:~$ curl http://localhost
<html><body><h1>It works!</h1></body></html>
test@ubuntu:~$
```

> **컨테이너 정지**  
> 컨테이너 정지시 ContainerID 를 사용합니다.  
```no-highlight
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
9e02908f90d2        httpd               "httpd-foreground"   22 minutes ago      Up 22 minutes       0.0.0.0:80->80/tcp   httpd-test
test@ubuntu:~$ docker stop 9e02908f90d2
9e02908f90d2
test@ubuntu:~$
```

> **컨테이너 재사용**  
> docker start ContainerID 로 컨테이너를 실행 할수 있습니다.  
> ContainerID 의 경우 docker ps -a 로 확인 가능합니다.  
```no-highlight
test@ubuntu:~$ docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS                          PORTS               NAMES
9e02908f90d2        httpd               "httpd-foreground"   23 minutes ago      Exited (0) About a minute ago                       httpd-test
test@ubuntu:~$ docker start 9e02908f90d2
9e02908f90d2
test@ubuntu:~$
```

> 컨테이너 재사용시 동일한 옵션으로 실행되는것을 확인 할수 있습니다.  
```no-highlight
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                NAMES
9e02908f90d2        httpd               "httpd-foreground"   25 minutes ago      Up About a minute   0.0.0.0:80->80/tcp   httpd-test
test@ubuntu:~$
```

> **컨테이너 삭제**  
> 컨테이너 삭제시 rm 으로 삭제 하시면 됩니다.  
```no-highlight
test@ubuntu:~$ docker rm 9e02908f90d2
9e02908f90d2
```

> **Mysql 5.7 컨테이너 실행**  
> -d 백그라운드 실행  
> -p 3306 외부포트:내부포트 연결  
> -e 컨테이너 내부에서 사용할 환경변수 설정  
> --name 컨테이너 이름 지정  
> mysql:5.7  mysql:Version 지정  
> 환경변수 값은 [Dockerhub Mysql Officeal repo](https://hub.docker.com/_/mysql/) 에서 확인 가능 합니다.  
```no-highlight
test@ubuntu:~$ docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name mysql mysql:5.7
Unable to find image 'mysql:5.7' locally
5.7: Pulling from library/mysql
802b00ed6f79: Pull complete
30f19a05b898: Pull complete
3e43303be5e9: Pull complete
94b281824ae2: Pull complete
51eb397095b1: Pull complete
54567da6fdf0: Pull complete
bc57ddb85cce: Pull complete
c7c0a9c25d8a: Pull complete
cce6c47ac3fc: Pull complete
499b9c7376c8: Pull complete
6c5e08e005ea: Pull complete
Digest: sha256:1d8f471c7e2929ee1e2bfbc1d16fc8afccd2e070afed24805487e726ce601a6d
Status: Downloaded newer image for mysql:5.7
71092d0a0aac614107e3cd60653f2d2ebd781cb2c0bdea3c4266e84b094ad9f5
test@ubuntu:~$
```

> **Mysql 접속 테스트**  
> 접속 테스트를 하기 위해서는 mysql5.7-client 설치가 필요합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get install -y mysql-client
```

> 접속테스트  
```no-highlight
test@ubuntu:~$ mysql -h127.0.0.1 -uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.23 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql>
```

> Mariadb 컨테이너 image 다운  

```no-highlight
test@ubuntu:~$ docker pull mariadb:latest
latest: Pulling from library/mariadb
124c757242f8: Pull complete
9d866f8bde2a: Pull complete
fa3f2f277e67: Pull complete
398d32b153e8: Pull complete
afde35469481: Pull complete
31f2ae82b3e3: Pull complete
3eeaf7e45ea6: Pull complete
716982328e17: Pull complete
34ce605c9036: Pull complete
4502ed9073c0: Pull complete
2afafbdf5a96: Pull complete
43d52b11dd31: Pull complete
30c7b70556f3: Pull complete
8b1b39f2f89a: Pull complete
41480b9319d7: Pull complete
Digest: sha256:b7894bd08e5752acdd41fea654cb89467c99e67b8293975bb5d787b27e66ce1a
Status: Downloaded newer image for mariadb:latest
test@ubuntu:~$
```

> **Mariadb 컨테이너 실행**  
> --name 이름지정 mariadb  
> -p 3306 외부:내부 포트 연결  
> -e 컨테이너 환경 변수 설정 (mysql root password 지정)  
> -d 백그라운드 실행  
> mariadb:latest mariadb Version 지정  

```no-highlight
test@ubuntu:~$ docker run --name mariadb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -d mariadb:latest
5b60cf8def76edcbc03603116e735f695413bf6d0cd22cb43c2e7d714cb2caeb
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
5b60cf8def76        mariadb:latest      "docker-entrypoint.s…"   5 seconds ago       Up 4 seconds        0.0.0.0:3306->3306/tcp   mariadb
test@ubuntu:~$
```

> **Mariadb 컨테이너 접속**  
> password 입력시 -e 로 지정한 mariadb password 를 입력 합니다.  
```no-highlight
test@ubuntu:~$ mysql -h127.0.0.1 -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 5.5.5-10.3.9-MariaDB-1:10.3.9+maria~bionic mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status;
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          8
Current database:
Current user:           root@172.17.0.1
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.5.5-10.3.9-MariaDB-1:10.3.9+maria~bionic mariadb.org binary distribution
Protocol version:       10
Connection:             127.0.0.1 via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 3 min 15 sec

Threads: 7  Questions: 4  Slow queries: 0  Opens: 17  Flush tables: 1  Open tables: 11  Queries per second avg: 0.020
--------------

mysql> ^DBye
test@ubuntu:~$
```

> **System rebooting 시 자동실행**  
> 컨테이너 실행시 run 옵션에서  --restart=always 옵션을 사용하면  
> 시스템 리부팅시에도 컨테이너가 자동으로 실행 됩니다.  
> 모든 이미지가 되는것은 아니며 일부 이미지의 경우 --restart=always 가 실행 안될수 있습니다.  
```no-highlight
test@ubuntu:~$ docker run --restart=always -d -p 80:80 --name nginx nginx:latest
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
802b00ed6f79: Pull complete
c16436dbc224: Pull complete
683eac851b28: Pull complete
Digest: sha256:e8ab8d42e0c34c104ac60b43ba60b19af08e19a0e6d50396bdfd4cef0347ba83
Status: Downloaded newer image for nginx:latest
835ad911bdd56973a8fc1e4692460ba49f123e932a2010cc060b987a534f13a1
test@ubuntu:~$
```

> System rebooting 후 컨테이너 확인  
```no-highlight
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                NAMES
835ad911bdd5        nginx:latest        "nginx -g 'daemon of…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp   nginx
test@ubuntu:~$ sudo init 6

System rebooting 후 확인
test@ubuntu:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
835ad911bdd5        nginx:latest        "nginx -g 'daemon of…"   2 minutes ago       Up 10 seconds       0.0.0.0:80->80/tcp   nginx
test@ubuntu:~$
```

> **Docker update**  
> docker update 명령어를 통하여 restart 옵션을 변경 할수 있습니다.  
```no-highlight
test@ubuntu:~$ docker update --restart=no nginx
nginx
```

> [Docker run reference Site](https://docs.docker.com/engine/reference/run/)  
> docker reference Site 에서 자세한 옵션 정보를 확인 할수 있습니다.  


<br></br>  
