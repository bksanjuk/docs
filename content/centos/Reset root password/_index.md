+++
title = "Reset Root Password"
weight = 11
+++

## Reset root password

- root Password Reset  

{{% notice note %}}
Root password 분실시 참고 문서 입니다.  
grub 매뉴에서 rd.break 를 입력하여 부팅을 진행한후 password 를 변경 합니다.  
{{% /notice %}}

> grub 매뉴에서 Esc 키를 누릅니다.  
> 키보드 e를 눌러 설정을 편집 합니다.  


![S1](/centos/centos_tmp/setting/a001.jpg)
<br></br>


> linux16 라인에서 End 키를 눌러 마지막 라인으로 이동합니다.  
> rd.break 입력후 Ctrl + x 를 눌러 부팅을 진행 합니다.  

![S2](/centos/centos_tmp/setting/a002.jpg)
<br></br>


> password 를 변경 합니다.

![S4](/centos/centos_tmp/setting/a003.jpg)
<br></br>

```no-highlight
:/# chroot /sysroot
:/# passwd root

selinux update information ( selinux 사용시 설정 )
:/# touch /.autorelabel
:/# exit
:/# reboot
```
> 부팅후 바뀐 패스워드로 로그인을 합니다.

![S5](/centos/centos_tmp/setting/a004.jpg)

<br></br>