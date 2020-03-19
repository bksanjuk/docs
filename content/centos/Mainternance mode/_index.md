+++
title = "Mainternance Mode"
weight = 13
+++

## Mainternance mode

- what is meinternance mode  

{{% notice note %}}
보통의 경우 /etc/fstab Device 를 mount 하지 못하였을경우 mainternance mode 로 진입한후 System booting 이 되지 않습니다.  
{{% /notice %}}

> 메인터넌스 모드로 빠졌을 경우 root 패스워드를 입력하여 system 에 진입할수 있습니다.

![M1](/centos/centos_tmp/setting/M1.png)

<br></br>

> system 에 진입후 /etc/fstab 확인 및 마운트 포인트를 확인 합니다.  

![M2](/centos/centos_tmp/setting/M2.png)

<br></br>

> /etc/fstab 에 주석 처리를 합니다.

![M3](/centos/centos_tmp/setting/M3.png)

<br></br>

> system booting 후 정상적으로 로그인 되었는지 확인 합니다.

![M4](/centos/centos_tmp/setting/M4.png)
> System login 후 /var/log/message 등을 점검 합니다.

<br></br>


