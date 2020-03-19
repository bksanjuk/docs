+++
title = "User Management"
weight = 6
+++

## User 관리
* User 관리 명령어

|<center>명령어</center> | <center>설명</center>|<center> 비고 </center>|
|:-------|:-------:|-------:|
|**adduser**|사용자 추가를 위한 명령어||
|**rmuser**|사용자 제거를 위한 명령어||
|**chpass**|사용자 데이터베이스 정보를 변경하기 위한 명령어||
|**passwd**|사용자 암호 변경을 위한 명령어||
|**pw**| 사용자 계정추가시 사용하는 명령어||

> **adduser 명령어**  
> 사용자 추가시 사용합니다.  
> adduser 명령어를 이용하여 user 생성시 모든 설정을 할수 있습니다.  

```no-highlight
root@BSD11:~ # adduser  
Username: test_user   
 Full name: test 
 Uid (Leave empty for default):  
 Login group [test_user]:  
 Login group is test_user. Invite test_user into other groups? []:  
 Login class [default]:  
 Shell (sh csh tcsh nologin) [sh]:  
 Home directory [/home/test_user]:  
 Home directory permissions (Leave empty for default):  
 Use password-based authentication? [yes]:  
 Use an empty password? (yes/no) [no]:  
 Use a random password? (yes/no) [no]:  
 Enter password:  
 Enter password again:  
 Lock out the account after creation? [no]:  
 Username   : test_user  
 Password   : *****  
 Full Name  : test  
 Uid        : 1002  
 Class      :  
 Groups     : test_user  
 Home       : /home/test_user  
 Home Mode  :  
 Shell      : /bin/sh  
 Locked     : no  
 OK? (yes/no): yes  
 adduser: INFO: Successfully added (test_user) to the user database.  
 Add another user? (yes/no): no  
 Goodbye!  
```
<br></br>  

> **rmuser 명령어**  
> 사용자 삭제시 사용합니다.  

```no-highlight
root@BSD11:~ # rmuser test_user
Matching password entry:

test_user:$6$WPASTTtStxedNphn$6w/5DRC7qffdq1FHtyJOK.RQB5D0Qwwi8BSJYlwZ1eMFkEz/Ao7B/vSoNbuY5.IsQZsOV6oy8v2c.6n7tjw8Z/:1002:1002::0:0:test:/home/test_user:/bin/sh

Is this the entry you wish to remove? yes
Remove user's home directory (/home/test_user)? yes
Removing user (test_user): mailspool home passwd.
```
<br></br>  

> **chpass 명령어**  
> 사용자 데이터베이스 정보변경시 사용합니다.  
> 사용자의 홈디렉토리 shell 변경시 사용합니다.  

```no-highlight
root@BSD11:~ # chpass
#Changing user information for root.
Login: root
Password: $6$Q1bGeweEzkm6tyUZ$uInwQ2Jir8CSz7MIOzbubRFLQtGti24.Qg9raUb..Ha6PZ4VqK
6gf.OhaBMrcNVd3S1VfH4FHyIQn6XjPtNY/0
Uid [#]: 0
Gid [# or name]: 0
Change [month day year]:
Expire [month day year]:
Class:
Home directory: /root
Shell: /bin/csh
Full Name: Charlie &
Office Location:
Office Phone:
Home Phone:
Other information:
```
<br></br>  


> **passwd 명령어**    
> user passwd 를 변경 합니다.  
> pw 명령어로 user 를 생성시 passwd 를 생성할수 있습니다.  

```no-highlight
root@BSD11:~ # passwd test_user
Changing local password for test_user
New Password:
Retype New Password:
root@BSD11:~ #
```
<br></br>  


> **pw 명령어**  
> user 생성 및 삭제시 사용하고 구룹설정을 할수 있습니다.  
> test 유저를 생성하고 wheel 구룹에 추가 합니다.  
> -m 옵션은 홈디렉토리를 생성하는 옵션 입니다.  
> pw user add test 시 홈디렉토리가 생성되지 않습니다.  

```no-highlight
root@BSD11:~ # pw user add test -g wheel -m
pw user add test 
root@BSD11:~ # pw user add test1
root@BSD11:~ # ls -al /home
lrwxr-xr-x  1 root  wheel  8 Sep 15 05:35 /home -> usr/home
root@BSD11:~ # ls -al /home/
total 18
drwxr-xr-x   3 root  wheel   3 Sep 23 03:32 .
drwxr-xr-x  16 root  wheel  16 Sep 15 05:34 ..
drwxr-xr-x   2 test  wheel  11 Sep 23 03:33 test
root@BSD11:~ #

test 유저를 삭제 합니다. 
root@BSD11:~ # pw user del test

test 유저를 wheel group 에 추가 합니다. 
root@BSD11:~ # pw user mod test -g wheel

test 유저의 홈디렉토리를 /home/test 로 설정 합니다. 
root@BSD11:~ # pw user mod test -m test
```
<br></br>  