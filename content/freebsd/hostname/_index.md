+++
title = "Hostname"
weight = 3
+++

## Hostname 설정  

> hostname 명령어를 통하여 hostname을 변경 할수 있습니다.  
> /etc/rc.conf 의 hostname line 를 수정해야 리부팅시 hostname 이 동일하게 적용됩니다.  

```no-highlight
root@BSD11:~# hostname test-bsd  
root@BSD11:~ # vi /etc/rc.conf
hostname="test-bsd"
```
<br></br> 