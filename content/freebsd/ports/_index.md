+++
title = "Ports"
weight = 5
+++

{{% notice note %}}
**PKG 관리**  
ports 시스템을 이용하여 설치할 경우 portsnap 명령어를 이용하여  
ports 디렉토리를 업데이트 할수 있습니다.  
일반적으로 의존성이 많이 걸리는 Web Server pkg 의 경우 ports 설치를 많이 하며  
의존성 없이 단순한 패키지를 설치 한다면 pkg 명령어를 이용하여 설치를 진행 합니다.  
ports 설치의 경우 속도가 느리며 pkg 명령어의 경우 yum 과 같은 바이너리 설치 방식이라 설치가 빠릅니다.  
{{% /notice %}}

* 최초 실행시  
```no-highlight
root@BSD11:~ # portsnap fetch
root@BSD11:~ # portsnap extract
root@BSD11:~ # portsnap fetch update
```

* 차후 실행시  
```no-highlight
root@BSD11:~ # portsnap fetch
root@BSD11:~ # portsnap update
root@BSD11:~ # portsnap fetch update
```
<br></br>  

{{% notice tip %}}
Ports install 시 주요 옵션  
{{% /notice %}}

* Ports 검색  

```no-highlight
root@BSD11:~ # whereis pkg$name
```

* Ports 디렉토리 검색 방법  
```no-highlight
root@BSD11:~ # cd /usr/ports/ && make search name=lsof  
```

* Ports 설치시 기본값으로 설치  
```no-highlight
root@BSD11:~ # make -DBATCH install  
```

* 의존성 패키지 옵션들을 미리 설정하여 패키지 설치  
```no-highlight
root@BSD11:~ # make config-recursive install  
```

* 설정값 삭제  
```no-highlight
root@BSD11:~ # make rmconfig  
```

* 의존성 패키지의 모든 설정값 삭제  
```no-highlight
root@BSD11:~ # make rmconfig-recursive  
```
<br></br>  


{{% notice info %}}
pkg 명령어를 통한 패키지관리  
{{% /notice %}}


* 패키지 검색  
```no-highlight
root@BSD11:~ # pkg search lsof
lsof-4.92.b,8                  Lists information about open files (similar to fstat(1))
p5-Unix-Lsof-0.0.5_2           Unix::Lsof -- a wrapper to the Unix lsof utility
root@BSD11:~ #
```

* 패키지 설치  
```no-highlight
root@BSD11:~ # pkg install lsof
```

* 패키지 삭제  
```no-highlight
root@BSD11:~ # pkg remove lsof
```

* 설치된 패키지 확인  
```no-highlight
root@BSD11:~ # pkg info
```