+++
title = "Repository"
weight = 6
+++

## 저장소 변경

> daum 또는 jaist 저장소 로 변경 하여 사용하실수 있습니다.  

```no-highlight
test@ubuntu:~$ sudi vi /etc/apt/sources.list

:%s/kr.archive.ubuntu.com/ftp.daum.net/g

or 

:%s/us.archive.ubuntu.com/ftp.jaist.ac.jp/g
저장소 확인
test@ubuntu:~$ sudo apt-get update

```