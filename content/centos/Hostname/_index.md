+++
title = "Hostname"
weight = 4
+++

## Hostname

* hostnamectl 명령어를 통하여 hostnmae 을 변경할수 있습니다.  
/etc/hostname 정상적으로 변경 되었는지 확인 합니다.  
```no-highlight
[root@localhost ~]# hostnamectl set-hostname centos7
[root@localhost ~]# cat /etc/hostname
centos7
```


- 대문자 사용시  

{{% notice tip %}}
/etc/hostname 수정후 시스템을 재시작 합니다.  
{{% /notice %}}

```no-highlight
[root@localhost ~]# vi /etc/hostname
CENTOS7
[root@localhost ~]# init 6
```