+++
title = "Dockerfile Use SSH Server"
weight = 6
+++


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


> Dockerfile build  
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