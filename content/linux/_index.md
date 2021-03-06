+++
title = "Linux"
weight = 2
pre = "<b>2. </b>"
+++

### History of Linux

{{% notice note %}}
리눅스는 핀란드 헬싱키 대학의 대학원생인 리누스 토발즈가 취미로 개발한 커널이었다.  
토발즈는 원래 앤드루 스튜어트 타넨바움 교수가 운영 체제 디자인을 가르치기 위해 만든 교육용 유닉스인 미닉스를 사용하고 있었는데,  
이 미닉스를 다른 사람이 함부로 개조하지 못하도록 제한을 두자, 미닉스의 기능에 만족하지 못했던 토발즈는 새로운 운영 체제를 개발하고자 했다.  
리눅스는 운영 체제 위에서 실행되는 터미널 에뮬레이터였다. 초기에는 시리얼 포트를 이용한 간단한 신호를 주고 받는 작업밖에 할 수 없었지만,  
토발즈는 디스크의 파일도 읽고, 쓰게하고 싶었다. 이처럼 완전한 파일 제어가 가능해지자, 토발즈는 이것을 포직스(POSIX)에 호환되는 운영 체제  
커널로 발전시키기로 마음먹고 이를 기반으로 리눅스를 개발하기 시작하였다.  
리눅스의 첫 번째 버전인 0.01은 1991년 9월 17일 뉴스 그룹을 통해 인터넷에 통해 공개되었고, 첫 공식버전인 0.02는 같은해 10월에 발표되었다.  
{{% /notice %}}


* **Linux distros (배포판 종류)**  
이밖에도 수많은 리눅스 배포판이 있습니다.  
![D1](/linux/linux_tmp/info/D1.png)  

<br></br>

### What is kernel
{{% notice info %}}
컴퓨터의 핵심 운영체제 시스템의 모든것을 완벽하게 제어,통제 하는 프로그램중에 하나 입니다.  
소프트 웨어 입출력 , 중앙처리 장치 의 데잍터 처리 명령 변환 , 키보드 , 모니터 , 프린터 및 스피커 와 같은 주변 장치 및 메모리를 처리 합니다.  
커널은 응용 프로그램 소프트웨어 를 컴퓨터 하드웨어에 연결 합니다.  
커널의 핵심 코드는 별도의 메모리 영역에 로드되며, 이 영역은 응용 프로그램 이나 운영 체제 중용하지 않는 다른부분에 액세스 하는것으로 부터 보호 합니다.  
커널 공간에서 프로세스 실행, 하드 디스크와 같은 하드웨어 장치 관리 및 인터럽트 처리를 합니다.  
{{% /notice %}}


* **Shell**  
> 리눅스 쉘은 각 운영체제와 사용자가 대화하는 중간 창구 역할을 합니다.  
> DOS의 command.com 및 윈도의 탐색기와 비슷합니다.  
> 리눅스에서 사용자와 운영체제가 통신하는 주요 수단입니다.  
> 유닉스 명령어 인터프리터로서 사용자가 입력한 명령을 해석하여 또 다른 프로그램을 수행하라는 명령으로 해석 한다.  


* **Kernel 과 Shell**  
> kernel 은 system 상에서 자원을 관리해주는 핵심동작을 합니다.  
> shell은 사용자와 kernel 에서 상주해서 돌아가는 프로그램 관리 및 kernel 관리 혹은 시스템에 직접 사용자가 명령을 내릴수
> 있는 interface 역활을 해 주는 환경을 제공해 줍니다.  
> 기본적으로 bash shell 을 많이 사용하고 있지만 Unix 환경 에서는 csh 나 ksh 등도 많이 사용하고 있습니다. 


* **본 쉘(sh)**  
> 리눅스를 사용하면서 일반적으로 가장 많이 사용된다고 볼수 있는 쉘은 개발자(Steven Borne:벨연구소)의 이름을 따라 명명된 본 쉘(Bourn Shell) 입니다.  
> 1979년에 개발되고 실행 파일이 /bin/sh 인 본 쉘의 최신판은 모든 리눅스 시스템에서 사용할수 있으며 본쉘 프로그램의 명령 이름은 sh 입니다.  
> 본쉘 보다 최근에 개발된 C 쉘이나 콘 쉘과 기능적인 면을 비교해 보면 본쉘에 미흡한 점이 있습니다.  
> 그중 가장큰 단점은 상호 대화형(interactive) 방식을 취하지 않고 있다는 점입니다. 이러한 상호 대화형 방식을 사용하고자 한다면 쉘을 바꿔서 작업하는것이 좋습니다.  

* **C 쉘(csh)**  
> C 쉘은 작업용 언어로 가장 많이 사용하는 언어인 C언어와 유사하고, 몇 가지 프로그램 개발에 편리한 기능도 내장하고 있어서 많이 보급이 되었습니다.  
> 현재 쓰이고 있는 리눅스 시스템에 C 쉘이 존재하고 본쉘을 사용중 C 쉘을 사용하고 싶을 때는 csh 명령으로 사용할수 있습니다.  
> 사용중인 쉘을 확인 하고 싶을 때는 ps 명령어를 통하여 확인 할수 있습니다.  


* **콘 쉘(ksh)**  
> 콘 쉘(Korn Shell) 은 ksh 라는 약자로 불리기도 하며, 데이비드 콘(David Korn) 에 의하여 1983년에 개발된 유닉스 운영 체제의 명령어 해석기 프로그램이며 실행  
> 파일은 /bin/ksh 입니다. 콘쉘은 유닉스에서 제공되는 원래의 쉘과 호환성이 있으며, 사용하기 편리하고 기능이 탁원하기 때문에 널리 사용되고 있습니다.  
> 일반적으로 유닉스에서 가장 많이 사용되고 있는 쉘입니다. 또한, 명령행 편집기능을 제공 합니다.  

* **배 시(bash)**  
> 배시 (Bash, Bourne-Agin Shell) 는 브라이언 폭스(Brain Fox) 외 몇몇 사랑에 의하여 1899년에 개발되었으며 실행 파일은 /bin/bash 입니다.  
> sh 호환의 명령 언어 해석기로 표준 입력 또는 파일로 부터 읽어들인 명령을 실행하고 또한 콘쉘과 C 쉘(ksh,csh) 부터 유용한 기능을 도입하였습니다.  
> IEEE POSIX 쉘과 도구 명세(IEEE Working Group 1003.2) 에 호환되도록 쉘을 구현하겠다는 목표를 가지고 있다.  
> 처음 로그인을 하였을때 디폴트로 주어지는 쉘이 배시이며, 또 여러 글자 옵션을 해석하며 이옵션을 인식시키려면 명령어행에서 단일 글자 옵션보다 앞에 적어야 한다.  
> 쉘의 경로 설정 파일 /etc/shells 파일을 열어보면 사용할수 있는 쉘들의 경로를 설정되어 있다.  

* **Z 쉘(zsh)**  
> Z 셸(Z shell, zsh)은 상호작용 로그인 셸이자 셸 스크립트를 위한 강력한 명령 줄 인터프리터로 사용할 수 있는 유닉스 셸이다.  
Zsh는 bash, ksh, tcsh의 일부 기능을 포함하여 수많은 개선 사항이 갖추어진 확장형 본 셸입니다.  

<br></br>