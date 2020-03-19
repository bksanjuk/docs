+++
title = "Locale"
weight = 5
+++


## Locale
* 한글 language-pack 설치  

```no-highlight
test@ubuntu:~$ sudo apt-get install language-pack-ko
locale 수정
test@ubuntu:~$ sudo vi /etc/default/locale
LANG="ko_KR.UTF-8"
```

* SSH 재접속후 확인  
```no-highlight
test@ubuntu:~$ locale
LANG=ko_KR.UTF-8
LANGUAGE=en_US:en
LC_CTYPE="ko_KR.UTF-8"
LC_NUMERIC="ko_KR.UTF-8"
LC_TIME="ko_KR.UTF-8"
LC_COLLATE="ko_KR.UTF-8"
LC_MONETARY="ko_KR.UTF-8"
LC_MESSAGES="ko_KR.UTF-8"
LC_PAPER="ko_KR.UTF-8"
LC_NAME="ko_KR.UTF-8"
LC_ADDRESS="ko_KR.UTF-8"
LC_TELEPHONE="ko_KR.UTF-8"
LC_MEASUREMENT="ko_KR.UTF-8"
LC_IDENTIFICATION="ko_KR.UTF-8"
LC_ALL=
test@ubuntu:~$
```


