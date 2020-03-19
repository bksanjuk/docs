+++
title = "Remove All Containers"
weight = 2
+++

## Remove all Containers  

{{% notice warning %}}
명령어 입력시 주의가 필요합니다.  
모든 컨테이너 및 모든 이미지 자료가 삭제 됩니다.  
{{% /notice %}}

* 실행중인 모든 컨테이너 삭제  
```no-highlight
test@ubuntu:~/Workspace$ docker rm -f `docker ps -a -q`
```

* 모든 이미지 일괄 삭제  
```no-highlight
test@ubuntu:~/Workspace$ docker rmi -f `docker images`
```

* docker-compose 이미지 삭제  
```no-highlight
test@ubuntu:~/Workspace$ docker-compose down --rmi all
```

<br></br>