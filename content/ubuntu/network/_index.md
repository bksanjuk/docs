+++
title = "Network"
weight = 7
+++

## Network  

> Static 설정  
> (고정ip 를 사용하기 위한 설정)

```no-highlight
test@ubuntu:~$ sudo vi /etc/network/interfaces
# The primary network interface
auto ens33
iface ens33 inet static
address 192.168.0.10
netmask 255.255.255.0
gateway 192.168.0.2
dns-nameservers 192.168.0.2

```

> DHCP 설정  
> (유동ip y를 사용하기 위한 설정)

```no-highlight
# The primary network interface
auto ens33
iface ens33 inet dhcp
```

> Network Service Restart  
> 변경된 network 설정을 적용하기위해서는 networking Daemon 의 재시작이 필요합니다.  

```no-highlight
test@ubuntu:~$ sudo systemctl restart networking
```
<br></br>  


