+++
title = "Dockerfile"
weight = 5
+++

## Dockerfile

{{% notice note %}}
Dockerfile 을 만들어 컨테이너 이미지를 커스터마이징 할수 있습니다.  
예를 들어 Web개발 환경 구성 이라던지, Version 에 맞는 어플리케이션 환경을 만들때 사용합니다.  
또한 Docker-compose 와 같이 사용되기도 합니다.  
[Dockerfile Reference Site](https://docs.docker.com/engine/reference/builder/#usage)  
{{% /notice %}}

> **Dockerfile 에서 사용되는 명령어**  
> 대소문자 상관 없이 사용할수 있지만 보통 대문자로 통일해서 사용합니다.  

|명령어    |설명    |명령어    |설명    |
|---------|--------|---------|--------|
|FROM     |베이스 이미지   |ADD |파일 및 디렉토리 추가   |
|MAINTAINER   |메인테이너 정보   |COPY   |파일 복사    |
|RUN   |스크립트 실행 또는 커맨드 실행  |VOLUME   |볼륨 연결    |
|CMD   |데몬 실행  |ENTRYPOINT   |데몬실행    |
|LABEL |라벨 설정   |USER   |사용자 설정    |
|EXPOSE |포트 연결  |WORKDIR   |작업 디렉토리 지정   |
|ENV   |환경변수    |   |   |


> **Dockerfile 작성**  
> nginx Dockerfile 을 작성합니다.  

```no-highlight
test@ubuntu:~$ vi Dockerfile
FROM ubuntu:16.04
MAINTAINER user01 <test@anonymouse.com>

RUN apt-get update
RUN apt-get install -y nginx
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN chown -R www-data:www-data /var/lib/nginx


VOLUME /data

WORKDIR /etc/nginx

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
```
> 사용된 Dockerfile 명령어 정리  


|명령어    	|설명    	|
|---	|---	|
|FROM   	|베이스 이미지     	|
|MAINTAINER   	|메인테이너 정보    	|
|RUN   	|스크립트 실행 또는 커맨드 실행   	|
|VOLUME   	|볼륨 연결    	|
|EXPOSE   	|포트연결    	|



> **docker build**  
> docker build 명령어를 사용하여 이미지를 생성 합니다.  
```no-highlight
test@ubuntu:~$ docker build --tag nginx-test .
Sending build context to Docker daemon  2.048kB
Step 1/11 : FROM ubuntu:16.04
16.04: Pulling from library/ubuntu
3b37166ec614: Pull complete
504facff238f: Pull complete
ebbcacd28e10: Pull complete
c7fb3351ecad: Pull complete
2e3debadcbf7: Pull complete
Digest: sha256:45ddfa61744947b0b8f7f20b8de70cbcdd441a6a0532f791fd4c09f5e491a8eb
Status: Downloaded newer image for ubuntu:16.04
 ---> b9e15a5d1e1a
Step 2/11 : MAINTAINER user01 <test@anonymouse.com>
 ---> Running in c64bfeb21305
Removing intermediate container c64bfeb21305
 ---> 64b81e5d35fa
Step 3/11 : RUN apt-get update
 ---> Running in 20f06613ccd4
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [107 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
~중략 
```

> **docker 실행**  
> Dockerfile 을 이용하여 build 한 이미지를 실행합니다.  
> --name 으로 컨테이너 이름을 nginx-test 로 지정 합니다.  
> -d 백그라운드 옵션을 사용 합니다.  
> -p 외부:내부 포트를 연결 합니다.  
> -v /root/nginx/data 디렉토리와 컨테이너 /data 디렉토리를 연결합니다.  
> docker build 로 생성한 nginx-test 이미지를 이용 합니다.  
```no-highlight
test@ubuntu:~$ docker run --name web-service -d -p 80:80 -v /root/nginx/data:/data nginx-test
fbb79f241122a8b2d8b461d2d4bffec1bf4a1f16a695d274aca1a6921d8d5ea2
test@ubuntu:~/Workspace/web-service$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                         NAMES
fbb79f241122        nginx-test          "nginx"             2 seconds ago       Up 2 seconds        0.0.0.0:80->80/tcp, 443/tcp   web-service
test@ubuntu:~$

```

> web page 확인  
```no-highlight
test@ubuntu:~$ curl http://localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
test@ubuntu:~$
```
<br></br>  