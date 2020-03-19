+++
title = "Rescue Mode"
weight = 12
+++


## Rescue mode

- Rescue mode  

{{% notice note %}}
Rescue mode 는 시스템 장애시 DVD images 를 이용하여 시스템에 부팅 하여 리눅스 시스템을 복구 할때 사용합니다.  
본 문서에는 grub2 설치 부분이 포함 되어 있습니다.  
{{% /notice %}}



> BIOS 에서 CD-ROM 부팅으로 변경 합니다.  

- vmware BIOS 변경  

{{% notice tip %}}
power on to firmware 를 선택하여 Bios 에서 부팅을 변경 할수 있습니다.  
{{% /notice %}}

> BIOS 에서 부팅 순서를 CD-ROM Driver 를 First 로 선택 합니다.

![S5](/centos/centos_tmp/setting/S5.png)
<br></br>

> Troubleshooting 을 선택 합니다.

![S6](/centos/centos_tmp/setting/S6.png)
<br></br>

> Rescue a CentOS system 을 선택 합니다.

![S7](/centos/centos_tmp/setting/S7.png)
<br></br>


> Continue 를 선택 합니다.

![S8](/centos/centos_tmp/setting/S8.png)
<br></br>

> chroot /mnt/sysimage 디스크 마운트 된 root 파티션을 변경 합니다.

![S9](/centos/centos_tmp/setting/S9.png)
<br></br>


```no-highlight
bash-4.2# chroot /mnt/sysimage
bash-4.2# ls /dev/sd*
/dev/sda /dev/sda1 /dev/sda2 /dev/sda3
bash-4.2# ls /sbin/ | grep grub2
```

> grub2 bootloder 설치

```no-highlight
bash-4.2# /sbin/grub2-install /dev/sda
bash-4.2# exit
exit
sh-4.2# init 6
```

![S10](/centos/centos_tmp/setting/S10.png)

<br></br>