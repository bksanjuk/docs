+++
title = "Docker Compose"
weight = 7
+++


![docker1](/docker/docker_tmp/D2.png) 
## What is dcoker-compose

{{% notice note %}}
docker 의 경우 컨테이너 하나로 application 등의 컨테이너를 올리기에는 적합하나 여러대의  
컨테이너 구성시 어려움이 있습니다. Docker-compose 를 이용하면 여러대의 컨테이너를 하나로 관리 할수 있습니다.  
{{% /notice %}}


<br></br>

## Docker-compose install

> curl 명령어를 통하여 docker-compose 설치를 진행 합니다.  
> 설치가 끝난후 사용을 위하여 chmod 로 실행권한을 추가 합니다.  
> Docker-compose [Version](https://github.com/docker/compose/releases)  
> Install 참고 [Docker site](https://docs.docker.com/compose/install/)  

```no-highlight
test@ubuntu-docs:~$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
test@ubuntu-docs:~$ sudo chmod +x /usr/local/bin/docker-compose
```
<br></br>

## How to use Docker-compose  

> **Docker-compose Web-service**  
> docker-compose 를 이용하여 web-service 컨테이너를 구성 합니다.  
> Nginx 1.10.2 + pip71 + Mysql 5.7 컨테이너가 구성 되었습니다.  

> Web-service 디렉토리 구조  
```no-highlight
test@ubuntu:~/Workspace/web-service$ tree
.
├── docker-compose.yml
├── nginx
│   └── conf
│       └── default.conf
└── php
    ├── Dockerfile
    └── conf
        └── php.ini

4 directories, 4 files
test@ubuntu:~/Workspace/web-service$
```

> docker-compose.yml 파일 작성  

```no-highlight  
test@ubuntu:~/Workspace/web-service$ vi docker-compose.yml
version: '2'

services:
    mysql:
        image: mysql:5.7
        ports:
            - 3306:3306
        volumes:
            - ./mysql:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_USER: docker
            MYSQL_PASSWORD: docker
            MYSQL_DATABASE: docker

    nginx:
        image: nginx:1.10.2
        ports:
            - 80:80
        restart: always
        volumes:
            - ./nginx/conf:/etc/nginx/conf.d
            - ~/Projects:/code
        links:
            - php
        depends_on:
            - php

    php:
        build: php
        expose:
            - 9000
        restart: always
        volumes:
            - ./php/conf/php.ini:/usr/local/etc/php/conf.d/custom.ini
            - ~/Projects:/code
        links:
            - mysql
test@ubuntu:~/Workspace/web-service$
```

> docker-compose version 은 2 Version 을 이용합니다.  
> [version정보](https://docs.docker.com/compose/compose-file/compose-file-v2/)  
> services 항목의 경우 컨테이너를 지정 할수 있습니다.  
> mysql , nginx , php 컨테이너로 구성이 되어 있습니다.  



> **mysql 필드 설명**  

```no-highlight
    mysql:
        image: mysql:5.7
        ports:
            - 3306:3306
        volumes:
            - ./mysql:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_USER: docker
            MYSQL_PASSWORD: docker
            MYSQL_DATABASE: docker
```

|명령어    	|설명    	|
|---	|---	|
|image   	|mysql 5.7 버젼을 사용합니다.    	|
|ports   	|3306 외부포트와 내부포트를 연결 합니다.    	|
|volumes   	|Database 저장 위치 설정을 위하여 Volume 을 연결 합니다.    	|
|restart   	|리부팅시에도 자동으로 실행합니다.   	|
|environment   	|MYSQL_ROOT_PASSWORD mysql 관리자 password 를 설정합니다.    	|
|environment   	|MYSQL_USER user 를 생성합니다.    	|
|environment   	|MYSQL_PASSWORD user password 를 설정합니다.    	|
|environment   	|MYSQL_DATABASE user 데이터베이스를 생성합니다.    	|  


> **nginx 필드 설명**  

```no-highlight
   nginx:
        image: nginx:1.10.2
        ports:
            - 80:80
        restart: always
        volumes:
            - ./nginx/conf:/etc/nginx/conf.d
            - ~/Projects:/code
        links:
            - php
        depends_on:
            - php
```

|명령어    	|설명    	|
|---	|---	|
|image   	|nginx 1.10.2 버젼을 사용합니다.    	|
|ports   	|web-page 포트 80 포트를 외부 내부를 연결 합니다.    	|
|restart   	|리부팅시에도 자동으로 실행합니다.    	|
|volumes   	|nginx 설정디렉토리 conf.d 디렉토리를 연결하고 Webpage 디렉토리를 연결합니다.    	|
|links   	|php 컨테이너와 연결합니다.    	|
|depends_on   	|php 서비스간 종속성유지를 위하여 사용합니다.    	|


> **php 필드 설명**  
```no-highlight
    php:
        build: php
        expose:
            - 9000
        restart: always
        volumes:
            - ./php/conf/php.ini:/usr/local/etc/php/conf.d/custom.ini
            - ~/Projects:/code
        links:
            - mysql
```

|명령어    	|설명    	|
|---	|---	|
|build   	|Dockerfile 을 이용하여 build 합니다.    	|
|expose   	|외부 ports 접속이 아닌 내부 컨테이너 접속을 위하여 사용합니다.    	|
|restart   	|리부팅시에도 자동으로 실행합니다.    	|
|volumes   	|custom.ini 설정 파일 및 php code 디렉토리를 연결 합니다.    	|
|links   	|mysql 컨테이너와 연결합니다.    	|


{{% notice tip %}}
Docker-compose version 3 참고 사항  
links 옵션의 경우 docer-compose version 3 에서는 더이상 사용하지 않을것으로 보입니다.  
자세한 내용은 [Docker site](https://docs.docker.com/compose/compose-file/) 에서 확인 가능 합니다.  
{{% /notice %}}


> Link to containers in another service. Either specify both the service name and a link alias (SERVICE:ALIAS), or just the service name.  

{{% notice warning %}}
The --link flag is a legacy feature of Docker. It may eventually be removed. Unless you absolutely need to continue using it, we recommend that you use user-defined networks to facilitate communication between two containers instead of using --link. One feature that user-defined networks do not support that you can do with --link is sharing environmental variables between containers. However, you can use other mechanisms such as volumes to share environment variables between containers in a more controlled way.
{{% /notice %}}


> nginx default.conf 파일 생성  
```no-highlight
test@ubuntu:~/Workspace/web-service$ vi nginx/conf/default.conf
server {
    listen       80 default_server;
    server_name  localhost _;
    index        index.php index.html index.htm;
    root         /code;

    location / {
        try_files   $uri $uri/ /index.php?$query_string;
        autoindex on;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}


* php/Dockerfile 생성  
test@ubuntu:~/Workspace/web-service$ vi php/Dockerfile
FROM php:7.1-fpm
 
RUN apt-get update
 
# Some basic extensions
RUN docker-php-ext-install -j$(nproc) json mbstring opcache pdo pdo_mysql mysqli
 
# Curl
RUN apt-get install -y libcurl4-openssl-dev
RUN docker-php-ext-install -j$(nproc) curl
 
# GD
RUN apt-get install -y libpng-dev libjpeg-dev
RUN docker-php-ext-install -j$(nproc) gd
 
# Intl
RUN apt-get install -y libicu-dev
RUN docker-php-ext-install -j$(nproc) intl


* php.ini 파일 생성  

```no-highlight
test@ubuntu:~/Workspace/web-service$ vi php/conf/php.ini
display_errors = On
display_startup_errors = On
default_charset = "UTF-8"
html_errors = On
date.timezone = Asia/Seoul
```

> docker-compose 실행  
> docker-compose.yml 파일이 있는 위치에서 실행을 해야 합니다.  
```no-highlight
test@ubuntu:~/Workspace/web-service$ docker-compose up -d --build
```

> 컨테이너 동작 확인  
```no-highlight
test@ubuntu:~/Workspace/web-service$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
bd2fb3813e0c        nginx:1.10.2        "nginx -g 'daemon of…"   9 minutes ago       Up 9 minutes        0.0.0.0:80->80/tcp, 443/tcp         webservice_nginx_1
a3769c7d61b8        webservice_php      "docker-php-entrypoi…"   9 minutes ago       Up 9 minutes        9000/tcp                            webservice_php_1
42e79bd2f599        mysql:5.7           "docker-entrypoint.s…"   9 minutes ago       Up 9 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   webservice_mysql_1
test@ubuntu:~/Workspace/web-service$
```

> **phpinfo 페이지 생성**  
> 일반적으로 docker-compose 구성시 디렉토리별 서비스를 생성하여 컨테이너를 관리 합니다.  
> 특별한경우가 없다면 ~/workspace/service-name 별로 컨테이너를 생성하여 관리 합니다.  

```no-highlight
test@ubuntu:~/Workspace/web-service$ sudo vi ~/Projects/info.php
<?php phpinfo(); ?>
```

> web page 확인  

![docker3](/docker/docker_tmp/D3.png)  


<br></br>

## Dockerfile use ssh-server  

{{% notice note %}}
ssh-server 의 경우 sshfs 볼륨 연결시 유용하게 사용될수 있습니다.  
ftp 서버 운영시 외부의 공유 디렉토리 연결시 사용합니다.  
{{% /notice %}}

> **Dockerfile 생성**  
> ssh-server 디렉토리 생성후 Dockerfile 을 생성 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ssh-server$ vi Dockerfile
FROM ubuntu:16.04
MAINTAINER Sven Dowideit <SvenDowideit@docker.com>
 
#RUN apt-get update && apt-get install -y openssh-server
RUN apt-get update && apt-get install -y openssh-server \
&& mkdir /var/run/sshd \
&& useradd -ms /bin/bash test \
&& sed -ie 's/test:!/test:\$6\$pIPlIFrg\$WZRTmOoouwD6L\/IDgz8fw97agQjdeXticOJyayjUYwmiuNKkJE92rBtr0XDXhVE4rSi9PkWv8rtnA0rCuR6ts\//g' /etc/shadow \
&& sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
&& sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
 
#COPY data/* /.ROOT/
 
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
 
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

> userid 는 test 이며 password 는 test1234입니다.  
> ports 22 번 포트를 expose 하여 차후 실행시 포트를 지정 하면 됩니다.  


> **Dockerfile build**  
```no-highlight
test@ubuntu:~/Workspace/ssh-server$ docker build --tag ssh-server .
```


> images 확인  
```no-highlight
test@ubuntu:~/Workspace/ssh-server$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ssh-server          latest              3085e59c6e99        3 minutes ago       211MB
ubuntu              16.04               b9e15a5d1e1a        3 weeks ago         115MB
test@ubuntu:~/Workspace/ssh-server$
```

> **컨테이너 실행**  
> ssh-server 연결용 디렉토릴르 생성 합니다.  
> docker build 로 생성한 컨테이너를 실행 합니다.  
> /home/test/ftp 디렉토리의 경우 sshfs 연결 목적으로 사용합니다.  
```no-highlight
test@ubuntu:~/Workspace/ssh-server$ mkdir ~/ftp
test@ubuntu:~/Workspace/ssh-server$ docker run --name ssh-server -d -p 12341:22 -v /home/test/ftp:/home/test/ftp ssh-server
e8927799de7e841a151a006019fc62dc0d5daa85688ed569372fd651032f5dac
test@ubuntu:~/Workspace/ssh-server$
```

> 컨테이너 실행 확인  
```no-highlight
test@ubuntu:~/Workspace/ssh-server$ docker ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                   NAMES
e8927799de7e        ssh-server          "/usr/sbin/sshd -D"   50 seconds ago      Up 50 seconds       0.0.0.0:12341->22/tcp   ssh-server
test@ubuntu:~/Workspace/ssh-server$
```

> 컨테이너 접속 확인 
> 접속포트는 12341 입니다  
> test user로 접속 하고 패스워드는 test1234 입니다.  
```no-highlight
test@ubuntu:~$ ssh -p 12341 test@192.168.0.10
The authenticity of host '[192.168.0.10]:12341 ([192.168.0.10]:12341)' can't be established.
ECDSA key fingerprint is SHA256:c1Z28xljDnrQnEHOFHYyxwZyoehNTWqdAef9wpwmRDc.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[192.168.0.10]:12341' (ECDSA) to the list of known hosts.
test@192.168.0.10's password:
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

test@e8927799de7e:~$
```

> 추가적인 부분은 Dockerfile 을 수정 하여 커스터마이징 하여 사용하시면 됩니다.  

<br></br>

## Docker-compose use pure-ftpd ssh-server

{{% notice note %}}
Docker-compose 를 이용하여 pure-ftpd , sshfs 용 ssh-server 를 설정 합니다.  
{{% /notice %}}

{{% notice info %}}
참고사항  
pure-ftpd 컨테이너의 경우 Test시 문제가 있어 ubuntu Version 을 14.04 를 사용합니다.  
{{% /notice %}}


{{% notice warning %}}
vmware 사용시 NAT 환경이 아닌 Bridge 모드로 해야 합니다.  
NAT 환경 이용시 ftp에 접속을 못할수 있습니다.  
{{% /notice %}}

> ftp-server 디렉토리 구조  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ tree
.
├── docker-compose.yml
├── pure-ftpd
│   ├── Dockerfile
│   └── pureftpd.passwd
└── ssh-server
    └── Dockerfile

2 directories, 4 files
test@ubuntu:~/Workspace/ftp-server$
```

> ftp-date 디렉토리를 생성 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ sudo mkdir /ftp-data
```


> docker-compose.yml 파일생성  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ vi docker-compose.yml
version: '3'

services:
  pure-ftpd:
    build:
      context: pure-ftpd
    image: ftp/pure-ftpd:14.04
    container_name: ftpd
    ports:
      - "21:21"
      - "20000-20099:20000-20099"
    volumes:
      - "/ftp-data:/home/ftp"
    restart: always

  ssh-server:
    build:
      context: ssh-server
    image: ssh-server
    container_name: ssh-server
    ports:
      - "12341:22"
    volumes:
      - "/ftp-data:/home/test/ftp:ro"
    restart: always
```

> ftp 연결 포트의 경우 20000~20099 번까지 포트를 맵핑 합니다.  
> sshfs 사용 포트의 경우 12341 을 사용 합니다.  


> **ssh-server Dockerfile 생성**  
> test 유저를 생성하고 test1234 로 패스워드를 지정합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ vi ssh-server/Dockerfile
FROM ubuntu:16.04
MAINTAINER Sven Dowideit <SvenDowideit@docker.com>

#RUN apt-get update && apt-get install -y openssh-server
RUN apt-get update && apt-get install -y openssh-server \
&& mkdir /var/run/sshd \
&& useradd -ms /bin/bash test \
&& sed -ie 's/test:!/test:\$6\$pIPlIFrg\$WZRTmOoouwD6L\/IDgz8fw97agQjdeXticOJyayjUYwmiuNKkJE92rBtr0XDXhVE4rSi9PkWv8rtnA0rCuR6ts\//g' /etc/shadow \
&& sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config \
&& sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

#COPY data/* /.ROOT/

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```


> pure-ftpd Dockerfile 생성  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ vi pure-ftpd/Dockerfile

FROM ubuntu:14.04
 
MAINTAINER test@test
 
RUN apt-get update && \
apt-get install pure-ftpd openssl libpam-dev libcap2-dev libldap2-dev libmysqlclient-dev libmysqlclient15-dev libpq-dev libssl-dev po-debconf dpkg-dev debhelper -y
 
RUN mkdir /tmp/pure-ftpd/ && \
        cd /tmp/pure-ftpd/ && \
        apt-get source pure-ftpd && \
        cd pure-ftpd-* && \
        sed -i '/^optflags=/ s/$/ --without-capabilities/g' ./debian/rules && \
        dpkg-buildpackage -b -uc
RUN dpkg -i /tmp/pure-ftpd/pure-ftpd-common*.deb
RUN apt-get -y install openbsd-inetd
RUN dpkg -i /tmp/pure-ftpd/pure-ftpd_*.deb
RUN apt-mark hold pure-ftpd pure-ftpd-common
RUN cd /etc/pure-ftpd && \
adduser ftpd-data && \
mkdir /home/ftp && \
chown ftpd-data:ftpd-data -R /home/ftp && \
echo yes > ./conf/ChrootEveryone && \
echo yes > ./conf/DontResolve && \
echo yes > ./conf/NoChmod && \
echo yes > ./conf/ProhibitDotFilesWrite && \
echo yes > ./conf/CustomerProof && \
echo '20000 20099' > ./conf/PassivePortRange && \
echo ',21' > ./conf/Bind && \
echo '2' > ./conf/TLS && \
openssl req -x509 -nodes -days 1825 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem -subj "/C=KR/ST=SouthKorea/L=Seoul/O=test.com/OU=IT Department/CN=ftp.test.com"
ADD pureftpd.passwd /etc/pure-ftpd/
RUN pure-pw mkdb
RUN ln -sf /dev/stdout /var/log/pure-ftpd/transfer.log
CMD /usr/sbin/pure-ftpd -l puredb:/etc/pure-ftpd/pureftpd.pdb -x -u 30 -H -S ,21 -O clf:/var/log/pure-ftpd/transfer.log -Z -A -p 20000:20099 -E -R -8 UTF-8 -Y 2
EXPOSE 21/tcp 20000-20099/tcp
```

> **pureftpd.passwd 파일생성**  
> ftp user 생성시 pureftpd.passwd 파일을 수정하시면 됩니다.  
> User test / Password test1234 생성  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ vi pure-ftpd/pureftpd.passwd
test:$1$P4DB31/0$iIPf8.YJ6XP6k1zsmQtJq1:1000:1000::/home/ftp/./::::::::::::
```

> **컨테이너 실행**  
> pure-ftpd 컨테이너와 ssh-server 컨테이너를 실행 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker-compose up -d --build
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
02161765e92c        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   21 seconds ago      Up 17 seconds       0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
f3e224722db6        ssh-server            "/usr/sbin/sshd -D"      21 seconds ago      Up 20 seconds       0.0.0.0:12341->22/tcp                                      ssh-server
test@ubuntu:~/Workspace/ftp-server$
```
<br></br>

> **FTP Client 에서 접속**  
> 스크린샷에서는 192.x.x.x 로 되어 있지만 NAT 환경에서는 정상적으로 접속이 안됩니다.  
> ftp 접속시 인증서가 나오며 이후 세션에서 항상 신뢰를 체크 합니다.  

![docker4](/docker/docker_tmp/D4.png)  
<br></br>

> **FTP upload Tesst**  
> upload Test 를 진행합니다.  

![docker5](/docker/docker_tmp/D5.png)  


> **FTP User 생성**  
> ftp 유저를 생성하기 위해서는 컨테이너로 접속후 생성 해야 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
8497bf8410c5        ssh-server            "/usr/sbin/sshd -D"      7 minutes ago       Up 7 minutes        0.0.0.0:12341->22/tcp                                      ssh-server
039250964c0f        ftp/pure-ftpd:14.04   "/bin/sh -c '/usr/sb…"   7 minutes ago       Up 7 minutes        0.0.0.0:21->21/tcp, 0.0.0.0:20000-20099->20000-20099/tcp   ftpd
test@ubuntu:~/Workspace/ftp-server$ docker exec -it 039250964c0f /bin/bash
root@039250964c0f:/# pure-pw useradd test1 -u ftpd-data -g ftpd-data -d /home/ftp
Password:
Enter it again:
root@039250964c0f:/# pure-pw mkdb
root@039250964c0f:/# cat /etc/pure-ftpd/pureftpd.passwd
test:$1$P4DB31/0$iIPf8.YJ6XP6k1zsmQtJq1:1000:1000::/home/ftp/./::::::::::::
test1:$1$RlCsZSv0$CrDL8oyE5eJG0/uUKhAyi/:1000:1000::/home/ftp/./::::::::::::
root@039250964c0f:/#
```

> **pureftpd.passwd 파일 수정**  
> 컨테이너 내부로 접속하여 유저를 생성하여도 컨테이너 재시작시 해당 설정이 없어 지며,  
> pure-ftpd 디렉토리의 passwd 파일을 수정해 줘야 합니다.  
```no-highlight
test@ubuntu:~/Workspace/ftp-server$ cat pure-ftpd/pureftpd.passwd
test:$1$P4DB31/0$iIPf8.YJ6XP6k1zsmQtJq1:1000:1000::/home/ftp/./::::::::::::
test@ubuntu:~/Workspace/ftp-server$
```

<br></br>

## Docker-compose use Web-service  

{{% notice note %}}
How to use Docker-compose 내용의 경우 일반적으로 사용하는 내용에 대하여 설명한 내용입니다.  
실제로 Web-service 운영한다면 docker-compose.yml 파일 디렉토리 아래 web page 디렉토리가 있는것이 관리상  
편합니다. database 의 경우 동일 합니다.  
{{% /notice %}}

> 설정내용 설명은 [How to use Dcoker-compose](http://112.187.207.248/docker/docker/#how-to-use-docker-compose) 를 참고 해 주세요.  
> 설정 디렉토리 /home/test/Workspace/Web-service
> Workspcae/Web-service 디렉토리를 생성 합니다.  

> **Web-service 디렉토리 구조**  
```no-highlight
test@ubuntu:~/Workspace/Web-service$ tree
.
├── docker-compose.yml
├── mariadb
├── nginx
│   └── conf
│       └── default.conf
├── php
│   ├── Dockerfile
│   └── conf
│       └── php.ini
└── www

6 directories, 4 files
test@ubuntu:~/Workspace/Web-service$
```


> docker-compose.yml 파일  

```no-highlight
test@ubuntu:~/Workspace/Web-service$ vi docker-compose.yml
version: '2'

services:
    mariadb:
        image: mariadb:10.1
        ports:
            - 3306:3306
        volumes:
            - ./mariadb:/var/lib/mysql
        restart: always
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_USER: docker
            MYSQL_PASSWORD: docker
            MYSQL_DATABASE: docker

    nginx:
        image: nginx:latest
        ports:
            - 80:80
        restart: always
        volumes:
            - ./nginx/conf:/etc/nginx/conf.d
            - ./www:/code
        links:
            - php
        depends_on:
            - php

    php:
        build: php
        expose:
            - 9000
        restart: always
        volumes:
            - ./php/conf/php.ini:/usr/local/etc/php/conf.d/custom.ini
            - ./www:/code
        links:
            - mariadb
test@ubuntu:~/Workspace/Web-service$
```


> Nginx default.conf 파일  

```no-highlight
test@ubuntu:~/Workspace/Web-service$ cat nginx/conf/default.conf
server {
    listen       80 default_server;
    server_name  localhost _;
    index        index.php index.html index.htm;
    root         /code;

    location / {
        try_files   $uri $uri/ /index.php?$query_string;
        autoindex on;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
test@ubuntu:~/Workspace/Web-service$
```

> php/Dockerfile 파일  
```no-highlight
test@ubuntu:~/Workspace/Web-service$ vi php/Dockerfile
FROM php:7.1-fpm

RUN apt-get update

# Some basic extensions
RUN docker-php-ext-install -j$(nproc) json mbstring opcache pdo pdo_mysql mysqli

# Curl
RUN apt-get install -y libcurl4-openssl-dev
RUN docker-php-ext-install -j$(nproc) curl

# GD
RUN apt-get install -y libpng-dev libjpeg-dev
RUN docker-php-ext-install -j$(nproc) gd

# Intl
RUN apt-get install -y libicu-dev
RUN docker-php-ext-install -j$(nproc) intl
test@ubuntu:~/Workspace/Web-service$
```

> php/conf/php.ini 파일  

```no-highlight
test@ubuntu:~/Workspace/Web-service$ vi php/conf/php.ini
display_errors = On
display_startup_errors = On
default_charset = "UTF-8"
html_errors = On
date.timezone = Asia/Seoul
test@ubuntu:~/Workspace/Web-service$
```


> docker-compose 실행  

```no-highlight  
test@ubuntu:~/Workspace/Web-service$ docker-compose up -d --build
```


> 컨테이너 상태 확인  
```no-highlight
test@ubuntu:~/Workspace/Web-service$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
47c7a871f381        nginx:latest        "nginx -g 'daemon of…"   18 seconds ago      Up 15 seconds       0.0.0.0:80->80/tcp       webservice_nginx_1
39cb0e8c6212        webservice_php      "docker-php-entrypoi…"   19 seconds ago      Up 17 seconds       9000/tcp                 webservice_php_1
dcbfcbc4cb03        mariadb:10.1        "docker-entrypoint.s…"   20 seconds ago      Up 18 seconds       0.0.0.0:3306->3306/tcp   webservice_mariadb_1
test@ubuntu:~/Workspace/Web-service$
```  


> phpinfo 확인  
```no-highlight
test@ubuntu:~/Workspace/Web-service$ vi www/test.php
<?php phpinfo(); ?>
```

> web page 확인  

![docker6](/docker/docker_tmp/D6.png)  

<br></br>

{{% notice tip %}}
Wordpress 사용시 참고  
DB 사용시 외부에서 사용하지 않는다면 expose 만 하여 사용합니다.  
Wordpress 사용시 컨테이너 이름으로 host를 지정하여 사용하시면 됩니다.  
{{% /notice %}}
```no-highlight
test@ubuntu:~/Workspace/Web-service$ cat docker-compose.yml
version: '2'

services:
    mariadb:
        image: mariadb:10.1
        expose:
            - 3306
~ 중략
```

> 컨테이너 이름 확인  
```no-highlight
test@ubuntu:~/Workspace/Web-service$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
9cdfc297133f        nginx:latest        "nginx -g 'daemon of…"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp   webservice_nginx_1
774f8cfc9466        webservice_php      "docker-php-entrypoi…"   5 minutes ago       Up 5 minutes        9000/tcp             webservice_php_1
a4327cb937ac        mariadb:10.1        "docker-entrypoint.s…"   5 minutes ago       Up 5 minutes        3306/tcp             webservice_mariadb_1
test@ubuntu:~/Workspace/Web-service$
```

> Wordpress 입력 참고  
> 데이터베이스 호스트 : webservice_mariadb_1 (DB 컨테이너 이름을 입력합니다.)  

![docker1](/docker/docker_tmp/D7.png)  
<br></br>


> **mariadb 명령어 참고**  
> docker-compose 에서 mariadb expose 설정을 하였을시에는 직접 컨테이너로 접속하여 db 를 생성해야 합니다.  
> environment 에서 사용할 유저/데이터 베이스를 미리 생성하여도 됩니다.  

```no-highlight
test@ubuntu:~/Workspace/Web-service$ mysql -h127.0.0.1 -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 24
Server version: 5.5.5-10.1.36-MariaDB-1~bionic mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> create database wp;
Query OK, 1 row affected (0.00 sec)

mysql> GRANT ALL ON wp.* TO 'wp'@'%' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> quit;
Bye



database 삭제 
mysql> drop database wp;

유저 확인 
mysql>  SELECT User, Host FROM mysql.user WHERE User='wp' AND Host='%';
+------+------+
| User | Host |
+------+------+
| wp   | %    |
+------+------+
1 row in set (0.00 sec)

유저 삭제 
mysql> DROP USER 'wp'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql>  SELECT User, Host FROM mysql.user WHERE User='wp' AND Host='%';
Empty set (0.00 sec)

mysql>
```

<br></br>


## Docker-compose use nginx-proxy  

{{% notice note %}}
nginx-proxy  
Docker 로 여러대의 Web-Service 운영시 nginx-proxy 를 이용하여  
Multi Web-Service 를 구성할수 있습니다.  
{{% /notice %}}



> **nginx-proxy multi-wordpress site**  
> 구성정보  
> [draw.io](https://www.draw.io/) 

![docker8](/docker/docker_tmp/D8.png)
<br></br>  


> nginx-proxy docker-compose.yml 파일생성  

```no-highlight
test@ubuntu:~/Workspace/nginx-proxy$ vi docker-compose.yml
## notice ##  $ docker network create nginx-proxy
version: "3"

services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    container_name: nginx-proxy
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    restart: always

networks:
  default:
    external:
      name: nginx-proxy
```


> docker network 생성  
```no-highlight
test@ubuntu:~/Workspace/nginx-proxy$ docker network create nginx-proxy
3cbc08acdd956c3e86378114c1958949ffa01515ffbb27ad124240692d3f3f46
test@ubuntu:~/Workspace/nginx-proxy$ docker network ls
NETWORK ID          NAME                 DRIVER              SCOPE
5d0bb683d2e4        bridge               bridge              local
50afc3109ff3        ftpserver_default    bridge              local
f479dbb309b5        host                 host                local
3cbc08acdd95        nginx-proxy          bridge              local
ea229616bede        none                 null                local
5ba40d49b37c        webservice_default   bridge              local
test@ubuntu:~/Workspace/nginx-proxy$
```

> nginx-proxy 컨테이너 실행  
```no-highlight
test@ubuntu:~/Workspace/nginx-proxy$ docker-compose up -d --build
```



> **blog1.test.com 컨테이너 생성**  
> blog1.test.com 컨테이너 에서 사용할 디렉토리 및 docker-compose.yml 파일을 생성합니다.  
> VIRTUAL_HOST 부분에서 사용할 도메인을 설정 합니다.  
```no-highlight
test@ubuntu:~/Workspace$ mkdir -p blog1/web-data
test@ubuntu:~/Workspace$ mkdir -p blog1/db-data
test@ubuntu:~/Workspace/blog1$ vi docker-compose.yml
version: "3"

services:
  db:
     image: mariadb
     volumes:
       - ./db-data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: test1234
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: test1234
     container_name: wp_blog_db

  wordpress:
     image: wordpress:latest
     expose:
       - 80
     restart: always
     volumes:
       - ./web-data:/var/www/html
     environment:
       VIRTUAL_HOST: blog1.test.com
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: test1234
     container_name: wp_blog

networks:
  default:
    external:
      name: nginx-proxy

```

> blog1.test.com 컨테이너 실행  
```no-highlight
test@ubuntu:~/Workspace/blog1$ docker-compose up -d --build
```

> web page 확인  
> blog1.test.com 으로 접속 합니다.  

![docker9](/docker/docker_tmp/D9.png)  
<br></br>  


> **blog2.test.com 컨테이너 생성**  
> blog2.test.com 컨테이너 에서 사용할 디렉토리 및 docker-compose.yml 파일을 생성합니다.  

```no-highlight
test@ubuntu:~/Workspace$ mkdir -p blog2/db-data
test@ubuntu:~/Workspace$ mkdir -p blog2/web-data
test@ubuntu:~/Workspace$ cd blog2/
test@ubuntu:~/Workspace/blog2$ vi docker-compose.yml
version: "3"

services:
  db2:
     image: mariadb
     volumes:
       - ./db-data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: test1234
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: test1234
     container_name: wp_blog_db2

  wordpress2:
     image: wordpress:latest
     expose:
       - 80
     restart: always
     volumes:
       - ./web-data:/var/www/html
     environment:
       VIRTUAL_HOST: blog2.test.com
       WORDPRESS_DB_HOST: db2:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: test1234
     container_name: wp_blog2

networks:
  default:
    external:
      name: nginx-proxy

```

> blog2.test.com 컨테이너 실행  
```no-highlight
test@ubuntu:~/Workspace/blog2$ docker-compose up -d --build
```


> web page 확인  

![docker1](/docker/docker_tmp/D10.png)  
<br></br>  







<br></br>  

## Docker-compose use nginx-proxy add nginx-web  

{{% notice note %}}
nginx-web Service  
nginx-web Service 를 추가 하는 방법을 기술 합니다.  
mariadb 컨테이너 생성시 설정파일을 추가 하여 컨테이너를 구동하도록 설정 하였습니다.  
{{% /notice %}}

> nginx-web Service 에서 사용할 디렉토리를 생성 합니다.  
```no-highlight
test@ubuntu:~/Workspace$ mkdir web1
test@ubuntu:~/Workspace$ cd web1/
test@ubuntu:~/Workspace/web1$ mkdir www-data
test@ubuntu:~/Workspace/web1$ mkdir -p mariadb/db-data
test@ubuntu:~/Workspace/web1$ mkdir -p nginx/conf
test@ubuntu:~/Workspace/web1$ mkdir -p php/conf
```  

> docker-compose.yml 파일을 생성합니다.  

```no-highlight
test@ubuntu:~/Workspace/web1$ vi docker-compose.yml
version: '2'
services:
  mysql:
    image: mariadb:10.3.1
    volumes:
      - ./mariadb/db-data/:/var/lib/mysql
      - ./mariadb/my.cnf:/etc/mysql/mariadb.conf.d/custom.cnf
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_USER: docker
      MYSQL_PASSWORD: docker
      MYSQL_DATABASE: docker
    container_name: mariadb-www

  nginx-www:
    image: nginx:latest
    restart: always
    volumes:
      - ./nginx/conf:/etc/nginx/conf.d
      - ./www-data:/www

    environment:
      - VIRTUAL_HOST=www.test.com
    container_name: nginx-www

  php:
    build: php
    expose:
      - 9000
    restart: always
    volumes:
      - ./php/conf/php.ini:/usr/local/etc/php/conf.d/custom.ini
      - ./www-data:/www
    container_name: php-www


networks:
  default:
    external:
      name: nginx-proxy
```

> nginx/conf/default.conf 파일 생성  
```no-highlight
test@ubuntu:~/Workspace/web1$ vi nginx/conf/default.conf
server {
    listen       80 default_server;
    server_name  localhost _;
    index        index.php index.html index.htm;
    root         /www;

    location / {
        try_files   $uri $uri/ /index.php?$query_string;
        autoindex on;
    }

    location ~ \.php$ {
        try_files $uri /index.php =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

> php/Dockerfile 파일 생성  
```no-highlight
test@ubuntu:~/Workspace/web1$ vi php/Dockerfile
FROM php:7.1-fpm

RUN apt-get update

# Some basic extensions
RUN docker-php-ext-install -j$(nproc) json mbstring opcache pdo pdo_mysql mysqli

# Curl
RUN apt-get install -y libcurl4-openssl-dev
RUN docker-php-ext-install -j$(nproc) curl

# GD
RUN apt-get install -y libpng-dev libjpeg-dev
RUN docker-php-ext-install -j$(nproc) gd

# Intl
RUN apt-get install -y libicu-dev
RUN docker-php-ext-install -j$(nproc) intl
```

> php/conf/php.ini 파일 생성  
```no-highlight
test@ubuntu:~/Workspace/web1$ vi php/conf/php.ini
display_errors = On
display_startup_errors = On
default_charset = "UTF-8"
html_errors = On
date.timezone = Asia/Seoul
```


> mariadb/my.cnf 파일 생성  
```no-highlight
test@ubuntu:~/Workspace/web1$ vi mariadb/my.cnf
# MariaDB database server configuration file.
#
# You can copy this file to one of:
# - "/etc/mysql/my.cnf" to set global options,
# - "~/.my.cnf" to set user-specific options.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html
 
# This will be passed to all mysql clients
# It has been reported that passwords should be enclosed with ticks/quotes
# escpecially if they contain "#" chars...
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.
[client]
port            = 3306
socket          = /var/run/mysqld/mysqld.sock
default-character-set = utf8mb4
 
 
# Here is entries for some specific programs
# The following values assume you have at least 32M ram
 
# This was formally known as [safe_mysqld]. Both versions are currently parsed.
[mysqld_safe]
socket          = /var/run/mysqld/mysqld.sock
nice            = 0
 
[mysqld]
#
# * Basic Settings
#
#user           = mysql
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
port            = 3306
basedir         = /usr
datadir         = /var/lib/mysql
tmpdir          = /tmp
lc_messages_dir = /usr/share/mysql
lc_messages     = en_US
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address           = 127.0.0.1
#
# * Fine Tuning
#
max_connections         = 100
connect_timeout         = 5
wait_timeout            = 600
max_allowed_packet      = 16M
thread_cache_size       = 128
sort_buffer_size        = 4M
bulk_insert_buffer_size = 16M
tmp_table_size          = 32M
max_heap_table_size     = 32M
#
# * MyISAM
#
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched. On error, make copy and try a repair.
myisam_recover_options = BACKUP
key_buffer_size         = 128M
#open-files-limit       = 2000
table_open_cache        = 400
myisam_sort_buffer_size = 512M
concurrent_insert       = 2
read_buffer_size        = 2M
read_rnd_buffer_size    = 1M
#
# * Query Cache Configuration
#
# Cache only tiny result sets, so we can fit more in the query cache.
query_cache_limit               = 128K
query_cache_size                = 64M
# for more write intensive setups, set to DEMAND or OFF
#query_cache_type               = DEMAND
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error logging goes to syslog due to /etc/mysql/conf.d/mysqld_safe_syslog.cnf.
#
# we do want to know about network errors and such
#log_warnings           = 2
#
# Enable the slow query log to see queries with especially long duration
#slow_query_log[={0|1}]
slow_query_log_file     = /var/log/mysql/mariadb-slow.log
long_query_time = 10
#log_slow_rate_limit    = 1000
#log_slow_verbosity     = query_plan
 
#log-queries-not-using-indexes
#log_slow_admin_statements
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id              = 1
#report_host            = master1
#auto_increment_increment = 2
#auto_increment_offset  = 1
#log_bin                        = /var/log/mysql/mariadb-bin
#log_bin_index          = /var/log/mysql/mariadb-bin.index
# not fab for performance, but safer
#sync_binlog            = 1
expire_logs_days        = 10
max_binlog_size         = 100M
# slaves
#relay_log              = /var/log/mysql/relay-bin
#relay_log_index        = /var/log/mysql/relay-bin.index
#relay_log_info_file    = /var/log/mysql/relay-bin.info
#log_slave_updates
#read_only
#
# If applications support it, this stricter sql_mode prevents some
# mistakes like inserting invalid dates etc.
#sql_mode               = NO_ENGINE_SUBSTITUTION,TRADITIONAL
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
default_storage_engine  = InnoDB
# you can't just change log file size, requires special procedure
#innodb_log_file_size   = 50M
innodb_buffer_pool_size = 256M
innodb_log_buffer_size  = 8M
innodb_file_per_table   = 1
innodb_open_files       = 400
innodb_io_capacity      = 400
innodb_flush_method     = O_DIRECT
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem
 
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
 
 
 
#
# * Galera-related settings
#
[galera]
# Mandatory settings
#wsrep_on=ON
#wsrep_provider=
#wsrep_cluster_address=
#binlog_format=row
#default_storage_engine=InnoDB
#innodb_autoinc_lock_mode=2
#
# Allow server to accept connections on all interfaces.
#
#bind-address=0.0.0.0
#
# Optional setting
#wsrep_slave_threads=1
#innodb_flush_log_at_trx_commit=0
 
[mysqldump]
quick
quote-names
max_allowed_packet      = 16M
 
[mysql]
#no-auto-rehash # faster start of mysql but no tab completion
 
[isamchk]
key_buffer              = 16M
 
#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#
!includedir /etc/mysql/conf.d/
```



> docker-compose 실행  
```no-highlight
test@ubuntu:~/Workspace/web1$ docker-compose up -d --build
```


> phpinfo 테스트  

```no-highlight
test@ubuntu:~/Workspace/web1$ vi www-data/test.php
<?php phpinfo(); ?>
```


> web page 확인  

![docker11](/docker/docker_tmp/D11.png)  
<br></br>

> 그누보드 설치 테스트  
> Host 정보 입력시 컨테이너 이름 으로 설정해야 합니다.  
> Host mariadb-www  

![docker12](/docker/docker_tmp/D12.png)  
<br></br>  

> 관리자 로그인후 확인  

![docker13](/docker/docker_tmp/D13.png)  


<br></br>