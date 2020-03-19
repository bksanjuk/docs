+++
title = "Apt Command"
weight = 8
+++

## apt command

> Ubuntu 에서는 PKG 관리를 위하여 apt-get , apt-cache 등을 사용 합니다.  
> 16.04 부터는 apt 명령어가 추가 되어 사용 할수 있습니다.  
> 특별한 기능을 apt-get 또는 apt-cache 에서 사용하지 않을 경우 apt 를 권장 하고 있습니다.  
> 개인적인 생각으로는 아직 까지는 익숙한 명령어를 통하여 PKG 를 관리 하시것이 좋을듯 싶습니다.  


{{% notice note %}}
**apt 명령어 소개**  
apt 명령어는 새로운 패키지 설치 , 기존 패키지 업그레이드 , 패키지 목록 생인 업데이트  
전체 ubuntu 업데그레이드 를 수행 할수 있습니다.  
Ubuntu 의 APT (Advanced Packaging Tool) 입니다.  
{{% /notice %}}

```no-highlight

> apt 명령어 list 를 보기 위해서 --help 로 command list 를 출력 합니다.  

test@ubuntu:~$ apt --help
apt 1.6.3ubuntu0.1 (amd64)
Usage: apt [options] command

apt is a commandline package manager and provides commands for
searching and managing as well as querying information about packages.
It provides the same functionality as the specialized APT tools,
like apt-get and apt-cache, but enables options more suitable for
interactive use by default.

Most used commands:
  list - list packages based on package names
  search - search in package descriptions
  show - show package details
  install - install packages
  remove - remove packages
  autoremove - Remove automatically all unused packages
  update - update list of available packages
  upgrade - upgrade the system by installing/upgrading packages
  full-upgrade - upgrade the system by removing/installing/upgrading packages
  edit-sources - edit the source information file

```
{{% notice tip %}}
기존 apt-cache 를 이용하여 search 를 할수 있지만 apt 명령어의 경우 search 로 패키지를 검색 할수 있습니다.  
install / remove 등등을 할수 있습니다.  
{{% /notice %}}

<br></br>  


> **패키지 설치**  
```no-highlight
test@ubuntu:~$ sudo apt install wget
```  

> **패키지 삭제**  
```no-highlight
test@ubuntu:~$ sudo apt remove wget
```  

> **패키지 검색**  
```no-highlight
test@ubuntu:~$ apt search nginx
```  

> **사용가능한 패키지목록 업데이트**  
apt-get update 와 동일합니다.  
```no-highlight
test@ubuntu:~$ sudo apt update
```  

> **System 에 설치된 패키지 upgrade**  
apt-get upgrade 와 동일합니다.  
```no-highlight
test@ubuntu:~$ sudo apt upgrade
```  

> **System 에 설치된 패키지 확인**  
```no-highlight
test@ubuntu:~$ apt list --installed
```  
<br></br>

{{% notice note %}}
apt-get 명령어  
일반적으로 ubuntu 에서 pkg 관리시 가장 많이 사용되는 명령어 입니다.  
apt-get 와 같이 사용되는 명령어를 보기 위하여 --help 를 입력합니다.  
{{% /notice %}}

```no-highlight
test@ubuntu:~$ sudo apt-get --help
apt 1.6.3ubuntu0.1 (amd64)
Usage: apt-get [options] command
       apt-get [options] install|remove pkg1 [pkg2 ...]
       apt-get [options] source pkg1 [pkg2 ...]

apt-get is a command line interface for retrieval of packages
and information about them from authenticated sources and
for installation, upgrade and removal of packages together
with their dependencies.

Most used commands:
  update - Retrieve new lists of packages
  upgrade - Perform an upgrade
  install - Install new packages (pkg is libc6 not libc6.deb)
  remove - Remove packages
  purge - Remove packages and config files
  autoremove - Remove automatically all unused packages
  dist-upgrade - Distribution upgrade, see apt-get(8)
  dselect-upgrade - Follow dselect selections
  build-dep - Configure build-dependencies for source packages
  clean - Erase downloaded archive files
  autoclean - Erase old downloaded archive files
  check - Verify that there are no broken dependencies
  source - Download source archives
  download - Download the binary package into the current directory
  changelog - Download and display the changelog for the given package

See apt-get(8) for more information about the available commands.
Configuration options and syntax is detailed in apt.conf(5).
Information about how to configure sources can be found in sources.list(5).
Package and version choices can be expressed via apt_preferences(5).
Security details are available in apt-secure(8).
                                        This APT has Super Cow Powers.
test@ubuntu:~$ 
```

> **사용가능한 패키지목록 업데이트**  
```no-highlight
test@ubuntu:~$ sudo apt-get update
```  

> **System 에 설치된 패키지 upgrade**  
```no-highlight
test@ubuntu:~$ sudo apt-get upgarde
```  

> **패키지 설치**  
```no-highlight
test@ubuntu:~$ sudo apt-get install wget
```  
 
> **패키지 삭제**  
```no-highlight
test@ubuntu:~$ sudo apt-get remove wget
```  

> **pkg 다운로드**  
실행된 디렉토리에 패키지를 다운로드 합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-get download wget
```  

> **pkg 검색**  
> pkg 검색 및 정보 확인의 경우 apt-caceh 명령어를 사용합니다.  
> 차후 apt 명령어로 통합이 예상 됩니다.  
> 저장소에서 pkg 를 검색 합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-cache search nginx
```


> **pkg 정보확인**  
> pkg 정보를 확인 할때 사용 되며, 주로 Version 정보를 확인합니다.  
```no-highlight
test@ubuntu:~$ sudo apt-cache search nginx
```
<br></br>