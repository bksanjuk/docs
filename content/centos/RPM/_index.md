+++
title = "RPM"
weight = 9
+++

## RPM

 - rpm 관리  

{{% notice note %}}
Redhat Package manager 로 rpm 파일로 설치 삭제 upgrade 시 사용되는 명령어 입니다.
특별한 경우가 아닌 온라인에서 운영중인 시스템의 경우 yum 으로 패키지를 관리 하며
yum 을 사용하지 못하는경우 또는 동일한 패키지 버젼 유지시 사용 됩니다.
{{% /notice %}}


<br></br>


* rpm  명령어 옵션

|<center>명령어</center> | <center>설명</center>|<center> 비고 </center>|
|:-------|:-------:|-------:|
|**i**|패키지 설치||
|**q**|패키지 정보질의||
|**U**|패키지 업그레이드||
|**e**|패키지 삭제||
|**V**|패키지 검증||


* rpm 패키지 설치

```no-highlight
[root@centos7 ~]# rpm -ivh wget-1.14-15.el7_4.1.x86_64.rpm
```

* rpm 패키지 삭제
```no-highlight
[root@centos7 ~]# rpm -e wget-1.14-15.el7_4.1.x86_64
```

* 패키지 업그레이드
```no-highlight
[root@centos7 ~]# rpm -Uvh wget-1.14-15.el7_4.1.x86_64.rpm
```

- 의존성 패키지의 경우  

{{% notice note %}}
rpm 파일의 경우 순서에 따라서 의존성 문제가 발생하는 경우가 종종 있습니다.  
해결방법은 다음과 같이 한 디렉토리에 Copy 하고 rpm 명령어 사용시 디렉토리를 지정하면 됩니다.  
{{% /notice %}}

```no-highlight
[root@centos7 ~]# rpm -Uvh /rpms*
```
<p></p>

* System 에 설치된 패키지 확인
```no-highlight
설치된 패키지 출력
[root@centos7 ~]# rpm -aq

설치된 패키지 검색
[root@centos7 ~]# rpm -aq |grep -i wget
wget-1.14-15.el7_4.1.x86_64
[root@centos7 ~]#

패키지가 설치된 날짜 확인
[root@centos7 ~]# rpm -aq --last
wget-1.14-15.el7_4.1.x86_64                   2018년 09월 29일 (토) 오전 09시 00분 48초
iwl7265-firmware-22.0.7.0-62.2.el7_5.noarch   2018년 09월 29일 (토) 오전 07시 48분 08초
iwl7260-firmware-22.0.7.0-62.2.el7_5.noarch   2018년 09월 29일 (토) 오전 07시 48분 08초
iwl6050-firmware-41.28.5.1-62.2.el7_5.noarch  2018년 09월 29일 (토) 오전 07시 48분 08초

```
<br></br>
