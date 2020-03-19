+++
title = "Install"
weight = 1
+++

## Install

{{% notice note %}}
FreeBSD 설치 방법을 기술 합니다.  
설치환경 : vm  
Cpu 1core / Memory 1G / Hdd 20G  
최소설치의 경우 Memory 32M 에서도 설치가 되지만 어플리케이션 운영 및 Ports 패키지  
설치시 문제가 될수 있음으로 1G 이상의 메모리를 사용하는것이 좋습니다.  
{{% /notice %}}

> **1. DVD image 를 이용하여 부팅을 합니다.**  
> (1번을 선택하여 설치를 진행합니다.)

![설치화면1](/freebsd/bsd_tmp/install/K-1.png)
<br></br>

> **2. Install 을 선택 합니다.**  

![설치화면2](/freebsd/bsd_tmp/install/K-2.png)  
<br></br>

> **3. keymap 을 설정 합니다.**  
> (United States of America ISO-8859-1 을 선택 합니다)  

![설치화면3](/freebsd/bsd_tmp/install/K-3.png)  
<br></br>     

> **4. keymap Selection 에서 Continue with us.iso.kbd**  
> keymap 을 선택 하여 설치를 계속 진행 합니다.  

![설치화면4](/freebsd/bsd_tmp/install/K-4.png)  
<br></br>

> **5. Set Hostname (차후에 /etc/rc.conf 수정을 통하여**  
> hostname 를 변경 할수 있습니다.) bsd11 을 입력 합니다.  

![설치화면5](/freebsd/bsd_tmp/install/K-5.png)  
<br></br>

> **6. Distribution Select**  
> (추가 설치 패키지 선택에서는 doc 및 src 를 추가 합니다.)  

![설치화면6](/freebsd/bsd_tmp/install/K-6.png)  
<br></br>     

> **7. Partitioning (파티션 작업 진행) Auto (UFS)를 선택 합니다.**  

![설치화면7](/freebsd/bsd_tmp/install/K-7.png)  
<br></br>     

> **8. Partition (Entire Disk 를 선택 합니다.)**  

![설치화면8](/freebsd/bsd_tmp/install/K-8.png)  
<br></br>     

> **9. Partition Scheme (MBR 을 선택 합니다.)**  
> BSD 나 GPT 를 선택 하여도 됩니다.  

![설치화면9](/freebsd/bsd_tmp/install/K-9.png)  
<br></br>     

> **10. Partition Editor**  
> (Finish 를 선택하여 다음을 세팅 합니다.)  

![설치화면10](/freebsd/bsd_tmp/install/K-10.png)  
<br></br>     

> **11. Confirmation**  
> (Commit 을 선택 하면 설치를 진행 합니다.)  

![설치화면11](/freebsd/bsd_tmp/install/K-11.png)  
<br></br>     

> **12. Fetching Distribution**  

![설치화면12](/freebsd/bsd_tmp/install/K-12.png)  
<br></br>     

> **13. Archive Extraction**  

![설치화면13](/freebsd/bsd_tmp/install/K-13.png)  
<br></br>     

> **14. 설치가 끝나면 세팅을 진행 합니다. Root 패스워드 입력**  

![설치화면14](/freebsd/bsd_tmp/install/K-14.png)  
<br></br>     

> **15. Network Configuration**  
> (네트워크 설정) OK 를 눌러 다음을 설정합니다.  

![설치화면15](/freebsd/bsd_tmp/install/K-15.png)  
<br></br>     

> **16. Network Configureation**  
> ( IPv4 사용유무 Yes를 선택 합니다.)  

![설치화면16](/freebsd/bsd_tmp/install/K-15.png)  
<br></br>     

> **17. Network Configureation**  
> ( DHCP 사용유무 Yes를 선택 합니다. ) 차후 /etc/rc.conf 에서 수정이 가능 합니다.  

![설치화면17](/freebsd/bsd_tmp/install/K-17.png)  
<br></br>     

> **18. Network Configureation**  
> ( IPv6 사용유무 No를 선택 합니다. )  

![설치화면17-1](/freebsd/bsd_tmp/install/K-17-1.png)  
<br></br>     

> **19. Time Zone Selector**  
> ( Asia 를 선택 합니다.)  

![설치화면19](/freebsd/bsd_tmp/install/K-19.png)  
<br></br>     

> **20. Countries in Asia**  
> (Korea, Republic of 를 선택 합니다.)  

![설치화면20](/freebsd/bsd_tmp/install/K-20.png)  
<br></br>     

> **21. Time & Date**  
> ( 시간과 날짜 설정)  

![설치화면21](/freebsd/bsd_tmp/install/K-21.png)  
<br></br>     

> **22. Time & Date**  
> ( 시간과 날짜 설정)  

![설치화면22](/freebsd/bsd_tmp/install/K-22.png)  
<br></br>     

> **23. System Configuration (시스템 설정)**  
> 차후 /etc/rc.conf 를 수정하여 변경 할수 있습니다.  

![설치화면23](/freebsd/bsd_tmp/install/K-23.png)  
<br></br>     

> **24. System Hardening ( Disable Sendmail service 를 선택 합니다.)**  
> 11 버전부터는 일부 서비스를 설치시 Disable 할수 있습니다.  

![설치화면24](/freebsd/bsd_tmp/install/K-24.png)  
<br></br>     

> **25. Add User Accounts (추가유저 생성 No 선택 )**  
> 차후 pw 명령어를 이용하여 유저를 생성 합니다.  

![설치화면25](/freebsd/bsd_tmp/install/K-25.png)  
<br></br>     

> **26. Final Configuration**  
> (Exit 를 선택하여 설치를 종료 합니다.)  

![설치화면26](/freebsd/bsd_tmp/install/K-26.png)  
<br></br>     

> **27. Complete**  
> (FreeBSD 11.1 의 설치가 완료 되었습니다. Reboot 를 선택합니다.)  

![설치화면27](/freebsd/bsd_tmp/install/K-27.png)  
<br></br>     