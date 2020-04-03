+++
title = "SELINUX"
weight = 8
+++


## What is SELINUX  
> SELinux(Security-Enhanced Linux)는 관리자가 시스템 액세스 권한을 효과적으로 제어할 수 있게 하는 Linux® 시스템용 보안 아키텍처입니다.  
미국 국가안보국(NSA)이 LSM(Linux Security Module)을 사용하여 Linux 커널에 대한 일련의 패치로 개발한것 이며, 2000년 오픈소스 커뮤니티에서 릴리즈 되어  
 2003년 업스트림 Linux kernel 로 통합되었습니다.  


{{% notice note %}}
본 문서에서는 SELINUX 를 활성화 하여 Apache , NFS , SAMBA 설정방법을 소개 합니다.  
{{% /notice %}}

{{% notice warning %}}
SELINUX 사용시 충분한 테스트를 하신후 사용을 권장 합니다.  
{{% /notice %}}

- SELINUX ENABLE  

>  SELINUX 를 사용하지 않는 경우 /etc/selinux/config 파일의 SELINUX=disabled 로 수정합니다.  
enforcing 로 설정 할경우 selinux 를 사용 하며, permissive 의 경우 selinux 위반 경고를 출력합니다.  

```no-highlight
[root@centos7 ~]# vi /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing  
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

{{% notice tip %}}
selinux 관련 명령어를 사용하기 위해서는 policycoreutils-python 패키지 설치가 필요 합니다.  
context 정보는 setools-console 패키지를 설치 하여 conext 정보를 확인 할수 있습니다.  
{{% /notice %}}

- policycoreutils-python 패키지 주요 명령어  

|명령어   |설명   |
|---|---|
|sestatus    |SELinux status tool   |
|getenforce   |get the current mode of SELinux   |
|setenforce   |modify the mode SELinux is running in   |
|semanage   |SELinux Policy Management tool   |
|chcon   |change file SELinux security context   |
|restorecon   |restore file(s) default SELinux security contexts   |
|getsebool   |get SELinux boolean value(s)   |

- setools-console 패키지 주요 명령어  

|명령어  |설명   |
|---|---|
|sesearch   |SELinux policy query tool   |
|seinfo   |SELinux policy query tool   |
|sediff   |SELinux policy difference tool   |

- semanage 명령어  

```no-highlight
[root@centos7 ~]# semanage -h
usage: semanage [-h]

                {import,export,login,user,port,ibpkey,ibendport,interface,module,node,fcontext,boolean,permissive,dontaudit}
                ...

semanage is used to configure certain elements of SELinux policy with-out
requiring modification to or recompilation from policy source.

positional arguments:
  {import,export,login,user,port,ibpkey,ibendport,interface,module,node,fcontext,boolean,permissive,dontaudit}
    import              Import local customizations
    export              Output local customizations
    login               Manage login mappings between linux users and SELinux
                        confined users
    user                Manage SELinux confined users (Roles and levels for an
                        SELinux user)
    port                Manage network port type definitions
    ibpkey              Manage infiniband ibpkey type definitions
    ibendport           Manage infiniband end port type definitions
    interface           Manage network interface type definitions
    module              Manage SELinux policy modules
    node                Manage network node type definitions
    fcontext            Manage file context mapping definitions
    boolean             Manage booleans to selectively enable functionality
    permissive          Manage process type enforcement mode
    dontaudit           Disable/Enable dontaudit rules in policy

optional arguments:
  -h, --help            show this help message and exit
[root@centos7 ~]#
```

- apache selinux  
> test 를 위하여 httpd 를 설치 하고 기동을 하였습니다.  
ls -Z 로 --context 를 확인시 httpd context 를 확인 할수 있습니다.  
/var/www 디렉토리의 html 의 경우 Default apache 디렉토리 이며, 기본적으로 selinux 가 설정되어 있습니다.  

```no-highlight
[root@centos7 ~]# ls -Z /var/www/
drwxr-xr-x. root root system_u:object_r:httpd_sys_script_exec_t:s0 cgi-bin
drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 html
[root@centos7 ~]#

- index.html 파일을 만들고 context 를 확인 합니다.  
디렉토리 권한을 상속하여 httpd context 가 설정 됩니다.  
[root@centos7 html]# vi index.html
[root@centos7 html]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:httpd_sys_content_t:s0 index.html
[root@centos7 html]#

```

{{% notice note %}}
apache 운영시에는 /var/www/html 디렉토리보다 별도의 디렉토리를 설정 한후 사용을 합니다.  
test.com 이라고 별도의 디렉토리를 만들고 APM 환경으로 Wordpress 환경을 구성 하여 테스트를 합니다.  
apache 마지막 테스트시 ssl 인증서를 사용한 https 설정을 진행 합니다.  
{{% /notice %}}

```no-highlight
# /test.com 디렉토리의 context 를 확입니다. 
apache 재시작시 정상적으로 동작 하지 않습니다. 

root@centos7 ~]# ls -Z /test.com/
drwxr-xr-x. root root unconfined_u:object_r:default_t:s0 logs
drwxr-xr-x. root root unconfined_u:object_r:default_t:s0 public_html
[root@centos7 ~]# systemctl restart httpd
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Fri 2020-03-27 08:12:47 KST; 6s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 3020 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
  Process: 3019 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
 Main PID: 3019 (code=exited, status=1/FAILURE)

Mar 27 08:12:47 centos7 systemd[1]: Starting The Apache HTTP Server...
Mar 27 08:12:47 centos7 systemd[1]: httpd.service: main process exited, code=exited, status=1/FAILURE
Mar 27 08:12:47 centos7 kill[3020]: kill: cannot find process ""
Mar 27 08:12:47 centos7 systemd[1]: httpd.service: control process exited, code=exited status=1
Mar 27 08:12:47 centos7 systemd[1]: Failed to start The Apache HTTP Server.
Mar 27 08:12:47 centos7 systemd[1]: Unit httpd.service entered failed state.
Mar 27 08:12:47 centos7 systemd[1]: httpd.service failed.
[root@centos7 ~]# 
```

## httpd 디렉토리 selinux 적용  

> semanage 명령어를 이용하여 /test.com 하위 디렉토리 까지 httpd_sys_rw_content_t context 로 설정 하고,  
restorecon 명령어로 /test.com 디렉토리에 context 를 적용합니다. restorecon 미 적용시 기존 context 를 유지 합니다.  
```no-highlight
[root@centos7 ~]# semanage fcontext -a -t httpd_sys_rw_content_t "/test.com(/.*)?"

[root@centos7 ~]# ls -Z /test.com/
drwxr-xr-x. root root unconfined_u:object_r:default_t:s0 logs
drwxr-xr-x. root root unconfined_u:object_r:default_t:s0 public_html
[root@centos7 ~]#

[root@centos7 ~]# restorecon -RFvv /test.com/
[root@centos7 ~]# ls -Z /test.com/
drwxr-xr-x. root root system_u:object_r:httpd_sys_rw_content_t:s0 logs
drwxr-xr-x. root root system_u:object_r:httpd_sys_rw_content_t:s0 public_html
[root@centos7 ~]#
```

- httpd 재시작 및 서비스 확인  
```no-highlight
[root@centos7 ~]# systemctl restart httpd
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2020-03-27 08:41:34 KST; 3s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 1709 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
 Main PID: 1731 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─1731 /usr/sbin/httpd -DFOREGROUND
           ├─1732 /usr/sbin/httpd -DFOREGROUND
           ├─1733 /usr/sbin/httpd -DFOREGROUND
           ├─1734 /usr/sbin/httpd -DFOREGROUND
           ├─1735 /usr/sbin/httpd -DFOREGROUND
           ├─1736 /usr/sbin/httpd -DFOREGROUND
           ├─1737 /usr/sbin/httpd -DFOREGROUND
           ├─1738 /usr/sbin/httpd -DFOREGROUND
           ├─1739 /usr/sbin/httpd -DFOREGROUND
           ├─1740 /usr/sbin/httpd -DFOREGROUND
           ├─1741 /usr/sbin/httpd -DFOREGROUND
           └─1742 /usr/sbin/httpd -DFOREGROUND

Mar 27 08:41:34 centos7 systemd[1]: Starting The Apache HTTP Server...
Mar 27 08:41:34 centos7 systemd[1]: Started The Apache HTTP Server.
[root@centos7 ~]#
```

- mariadb 디렉토리 변경  
> mariadb 의 Default 디렉토리는 /var/lib/mysql 입니다. /db-data 라고 별도로 db 를 지정 selinux context 를 설정 합니다.  
디렉토리 변경후 selinux context 설정 없이 mariadb 재기동이 정상적으로 되지 않습니다.  

```no-highlight
[root@centos7 ~]# systemctl restart mariadb
Job for mariadb.service failed because the control process exited with error code. See "systemctl status mariadb.service" and "journalctl -xe" for details.
[root@centos7 ~]#
[root@centos7 ~]# ls -Z /var/lib/mysql/
-rw-rw----. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 aria_log.00000001
-rw-rw----. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 aria_log_control
-rw-rw----. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 ibdata1
-rw-rw----. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 ib_logfile0
-rw-rw----. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 ib_logfile1
-rw-rw----. mysql mysql system_u:object_r:mysqld_db_t:s0 multi-master.info
drwx------. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 mysql
drwx------. mysql mysql unconfined_u:object_r:mysqld_db_t:s0 performance_schema
[root@centos7 ~]#

```

## mariadb 디렉토리 selinux 적용  
```no-highlight
[root@centos7 ~]# semanage fcontext -a -t mysqld_db_t "/db-data(/.*)?"
[root@centos7 ~]# restorecon -RFvv /db-data
```

- mariadb 재시작 및 서비스 확인  
```no-highlight
[root@centos7 ~]# systemctl restart mariadb
[root@centos7 ~]# systemctl status mariadb
● mariadb.service - MariaDB 10.1.44 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/mariadb.service.d
           └─migrated-from-my.cnf-settings.conf
   Active: active (running) since Fri 2020-03-27 08:58:34 KST; 4s ago
     Docs: man:mysqld(8)
           https://mariadb.com/kb/en/library/systemd/
  Process: 2297 ExecStartPost=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
  Process: 2253 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`/usr/bin/galera_recovery`; [ $? -eq 0 ]   && systemctl set-environment _WSREP_START_POSITION=$VAR || exit 1 (code=exited, status=0/SUCCESS)
  Process: 2251 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)
 Main PID: 2268 (mysqld)
   Status: "Taking your SQL requests now..."
   CGroup: /system.slice/mariadb.service
           └─2268 /usr/sbin/mysqld

Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] InnoDB:  Percona XtraDB (http://www.percona.com) 5.6.46-86.2 started...er 1616737
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] Plugin 'FEEDBACK' is disabled.
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] Recovering after a crash using tc.log
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] Starting crash recovery...
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] Crash recovery finished.
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140274927888128 [Note] InnoDB: Dumping buffer pool(s) not yet started
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] Server socket created on IP: '::'.
Mar 27 08:58:34 centos7 mysqld[2268]: 2020-03-27  8:58:34 140275675855104 [Note] /usr/sbin/mysqld: ready for connections.
Mar 27 08:58:34 centos7 mysqld[2268]: Version: '10.1.44-MariaDB'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MariaDB Server
Mar 27 08:58:34 centos7 systemd[1]: Started MariaDB 10.1.44 database server.
Hint: Some lines were ellipsized, use -l to show in full.
[root@centos7 ~]#
```


## https 설정  
> test.com 에 사설 인증서를 만들어 https 를 Test 하였습니다.  


```no-highlight
ssl 설정
<VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/test.crt
        SSLCertificateKeyFile /etc/pki/tls/private/test.key
        SSLCertificateChainFile /etc/pki/tls/certs/testca.crt
        <Directory /var/www/html/test.com/public_html>
        AllowOverride All
        </Directory>
        DocumentRoot /var/www/html/test.com/public_html
        ServerName test.com
       ServerAlias www.test.com
</VirtualHost>



# /etc/pki 디렉토리의 경우 cert_t context 로 설정 되어 있습니다.  
[root@centos7 conf.d]# ls -Z /etc/pki
drwxr-xr-x. root root system_u:object_r:cert_t:s0      CA
drwxr-xr-x. root root system_u:object_r:cert_t:s0      ca-trust
drwxr-xr-x. root root system_u:object_r:cert_t:s0      java
drwxr-xr-x. root root system_u:object_r:cert_t:s0      nssdb
drwxr-xr-x. root root system_u:object_r:cert_t:s0      nss-legacy
drwxr-xr-x. root root system_u:object_r:cert_t:s0      rpm-gpg
drwx------. root root system_u:object_r:cert_t:s0      rsyslog
drwxr-xr-x. root root system_u:object_r:cert_t:s0      tls
[root@centos7 conf.d]#


# /etc/pki/tls/certs 디렉토리의 testca , test.crt 파일의 경우 self signed certificate 을 생성 한 것입니다. 
cert_t context 로 자동 지정 됩니다.  
[root@centos7 ~]# ls -Z /etc/pki/tls/certs/
lrwxrwxrwx. root root system_u:object_r:cert_t:s0      ca-bundle.crt -> /etc/pki/ca-trust/extracted/pem/tls-ca-bundle.pem
lrwxrwxrwx. root root system_u:object_r:cert_t:s0      ca-bundle.trust.crt -> /etc/pki/ca-trust/extracted/openssl/ca-bundle.trust.crt
-rw-------. root root unconfined_u:object_r:cert_t:s0  localhost.crt
-rwxr-xr-x. root root system_u:object_r:bin_t:s0       make-dummy-cert
-rw-r--r--. root root system_u:object_r:cert_t:s0      Makefile
-rwxr-xr-x. root root system_u:object_r:cert_t:s0      renew-dummy-cert
-rw-r--r--. root root unconfined_u:object_r:cert_t:s0  testca.crt
-rw-r--r--. root root unconfined_u:object_r:cert_t:s0  testca.csr
-rw-r--r--. root root unconfined_u:object_r:cert_t:s0  testca.srl
-rw-r--r--. root root unconfined_u:object_r:cert_t:s0  test.crt
-rw-r--r--. root root unconfined_u:object_r:cert_t:s0  test.csr
[root@centos7 ~]#


# 별도의 디렉토리에 인증서를 생성 하였을 경우 동일하게 되는지 확인 해 보겠습니다.  
/root 디렉토리에 별도의 인증서 디렉토리를 만들고 아래와 같이 사설 인증서를 생성 합니다.  
인증서 생성시 /root 디렉토리 아래 만들어 자동적으로 admin_home_t 를 상속 받는것을 확인할수 있습니다.  
/root 디렉토리의
[root@centos7 ~]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 0
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
drwxr-xr-x. root root unconfined_u:object_r:admin_home_t:s0 sos
drwxr-xr-x. root root unconfined_u:object_r:admin_home_t:s0 ssl_config
drwxr-xr-x. root root unconfined_u:object_r:admin_home_t:s0 webca
[root@centos7 ~]#


[root@centos7 ~]# mkdir /root/webca
[root@centos7 ~]# openssl genrsa -aes256 -out /root/webca/webca.key 2048
Generating RSA private key, 2048 bit long modulus
...................+++
............+++
e is 65537 (0x10001)
Enter pass phrase for /root/webca/webca.key:
Verifying - Enter pass phrase for /root/webca/webca.key:
[root@centos7 ~]# ls -Z webca/
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 webca.key
[root@centos7 ~]#
~ 중략 


# /root 디렉토리와 동일한 context 를 상속 합니다.  
context 변경 없이 apache 에서 인증서 설정시 정상적으로 동작하는지 확인해 봅니다.  
[root@centos7 webca]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 webca.crt
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 webca.csr
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 webca.key
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 webca.srl
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 web.crt
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 web.csr
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 web.key
[root@centos7 webca]#


# httpd 재시작 하였을시 정상적으로 구동 되지 않습니다.  
23번 라인 확인시 ssl 설정 구문을 확인 할수 있습니다.  
setenforce 0 으로 selinux 설정을 Permissive 로 변경 하고 httpd 를 재시작 합니다. 
정상적으로 구동이 됩니다.
[root@centos7 ~]# systemctl restart httpd
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Fri 2020-04-03 22:49:49 KST; 3s ago                                <-- 구동상태에서 failed 를 확인 할수 있습니다.  
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 1669 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
  Process: 1668 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
 Main PID: 1668 (code=exited, status=1/FAILURE)

Apr 03 22:49:49 centos7 systemd[1]: Starting The Apache HTTP Server...
Apr 03 22:49:49 centos7 httpd[1668]: AH00526: Syntax error on line 23 of /etc/httpd/conf.d/vhost.conf:         <-- 23번 라인을 확인합니다.  
Apr 03 22:49:49 centos7 httpd[1668]: SSLCertificateFile: file '/root/webca/web.crt' does not exist or is empty
Apr 03 22:49:49 centos7 systemd[1]: httpd.service: main process exited, code=exited, status=1/FAILURE
Apr 03 22:49:49 centos7 kill[1669]: kill: cannot find process ""
Apr 03 22:49:49 centos7 systemd[1]: httpd.service: control process exited, code=exited status=1
Apr 03 22:49:49 centos7 systemd[1]: Failed to start The Apache HTTP Server.
Apr 03 22:49:49 centos7 systemd[1]: Unit httpd.service entered failed state.
Apr 03 22:49:49 centos7 systemd[1]: httpd.service failed.
[root@centos7 ~]#



# setenforce 0 설정후 httpd 재기동  
[root@centos7 ~]# setenforce 0
[root@centos7 ~]# systemctl restart httpd
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2020-04-03 22:51:52 KST; 5s ago                                           <--  httpd running 상태를 확인 할수 있습니다.  
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 1669 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
 Main PID: 1682 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─1682 /usr/sbin/httpd -DFOREGROUND
           ├─1683 /usr/sbin/httpd -DFOREGROUND
           ├─1684 /usr/sbin/httpd -DFOREGROUND
           ├─1685 /usr/sbin/httpd -DFOREGROUND
           ├─1686 /usr/sbin/httpd -DFOREGROUND
           ├─1687 /usr/sbin/httpd -DFOREGROUND
           ├─1688 /usr/sbin/httpd -DFOREGROUND
           ├─1689 /usr/sbin/httpd -DFOREGROUND
           ├─1690 /usr/sbin/httpd -DFOREGROUND
           ├─1691 /usr/sbin/httpd -DFOREGROUND
           ├─1692 /usr/sbin/httpd -DFOREGROUND
           └─1693 /usr/sbin/httpd -DFOREGROUND

Apr 03 22:51:52 centos7 systemd[1]: Starting The Apache HTTP Server...
Apr 03 22:51:52 centos7 systemd[1]: Started The Apache HTTP Server.
[root@centos7 ~]#


# cert_t 로 context 변경후 httpd 재시작  
/root/webca 디렉토리에 context 를 설정한 다음 httpd 구동이 정상적으로 되는것을 확인 할수 있습니다.  

[root@centos7 ~]# semanage fcontext -a -t cert_t "/root/webca(/.*)?"
[root@centos7 ~]# restorecon -RFvv /root/webca
restorecon reset /root/webca context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/webca.key context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/webca.csr context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/webca.crt context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/web.key context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/web.csr context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/web.crt context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
restorecon reset /root/webca/webca.srl context unconfined_u:object_r:admin_home_t:s0->system_u:object_r:cert_t:s0
[root@centos7 ~]#


# httpd 재기동  
[root@centos7 ~]# systemctl restart httpd
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2020-04-03 22:57:03 KST; 3s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 1162 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
 Main PID: 1680 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─1680 /usr/sbin/httpd -DFOREGROUND
           ├─1681 /usr/sbin/httpd -DFOREGROUND
           ├─1682 /usr/sbin/httpd -DFOREGROUND
           ├─1683 /usr/sbin/httpd -DFOREGROUND
           ├─1684 /usr/sbin/httpd -DFOREGROUND
           ├─1685 /usr/sbin/httpd -DFOREGROUND
           ├─1686 /usr/sbin/httpd -DFOREGROUND
           ├─1687 /usr/sbin/httpd -DFOREGROUND
           ├─1688 /usr/sbin/httpd -DFOREGROUND
           ├─1689 /usr/sbin/httpd -DFOREGROUND
           ├─1690 /usr/sbin/httpd -DFOREGROUND
           └─1691 /usr/sbin/httpd -DFOREGROUND

Apr 03 22:57:03 centos7 systemd[1]: Starting The Apache HTTP Server...
Apr 03 22:57:03 centos7 systemd[1]: Started The Apache HTTP Server.
[root@centos7 ~]#
```


> ssl 인증서 port 변경에 따른 selinux 테스트  
> http_port 는 80, 81, 443 등은 기본으로 사용 할수 있습니다. semanage port -l 으로 context 에 따른 사용 가능 포트를 확인 할수 있습니다.  

```no-highlight
[root@centos7 ~]# semanage port -l |grep -w http_port_t
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
[root@centos7 ~]#

ssl.conf Listen 443 에서 8280 / vhost.conf 의 443 에서 8280 로 변경 합니다.  


# httpd 재시작  
[root@centos7 ~]# systemctl restart httpd
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Fri 2020-04-03 23:07:19 KST; 3s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 2219 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
  Process: 2218 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
 Main PID: 2218 (code=exited, status=1/FAILURE)

Apr 03 23:07:19 centos7 httpd[2218]: (13)Permission denied: AH00072: make_sock: could not bind to address [::]:8280
Apr 03 23:07:19 centos7 httpd[2218]: (13)Permission denied: AH00072: make_sock: could not bind to address 0.0.0.0:8280
Apr 03 23:07:19 centos7 httpd[2218]: no listening sockets available, shutting down
Apr 03 23:07:19 centos7 httpd[2218]: AH00015: Unable to open logs
Apr 03 23:07:19 centos7 systemd[1]: httpd.service: main process exited, code=exited, status=1/FAILURE
Apr 03 23:07:19 centos7 kill[2219]: kill: cannot find process ""
Apr 03 23:07:19 centos7 systemd[1]: httpd.service: control process exited, code=exited status=1
Apr 03 23:07:19 centos7 systemd[1]: Failed to start The Apache HTTP Server.
Apr 03 23:07:19 centos7 systemd[1]: Unit httpd.service entered failed state.
Apr 03 23:07:19 centos7 systemd[1]: httpd.service failed.
[root@centos7 ~]#


#selinux port 추가후 재시작  
정상적으로 httpd 를 시작 할수 있습니다.  
[root@centos7 ~]# semanage port -a -t http_port_t -p tcp 8280
[root@centos7 ~]# semanage port -l |grep -w http_port_t
http_port_t                    tcp      8280, 80, 81, 443, 488, 8008, 8009, 8443, 9000
[root@centos7 ~]#

[root@centos7 ~]# systemctl start httpd
[root@centos7 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2020-04-03 23:09:00 KST; 4s ago
     Docs: man:httpd(8)
           man:apachectl(8)
  Process: 2219 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=1/FAILURE)
 Main PID: 2240 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─2240 /usr/sbin/httpd -DFOREGROUND
           ├─2241 /usr/sbin/httpd -DFOREGROUND
           ├─2246 /usr/sbin/httpd -DFOREGROUND
           ├─2247 /usr/sbin/httpd -DFOREGROUND
           ├─2248 /usr/sbin/httpd -DFOREGROUND
           ├─2249 /usr/sbin/httpd -DFOREGROUND
           ├─2250 /usr/sbin/httpd -DFOREGROUND
           ├─2251 /usr/sbin/httpd -DFOREGROUND
           ├─2252 /usr/sbin/httpd -DFOREGROUND
           └─2253 /usr/sbin/httpd -DFOREGROUND

Apr 03 23:09:00 centos7 systemd[1]: Starting The Apache HTTP Server...
Apr 03 23:09:00 centos7 systemd[1]: Started The Apache HTTP Server.
[root@centos7 ~]#

```


## NFS 설정  

> selinux01 NFS Server / selinux02 NFS Client  
NFS context 는 public_content_rw_t 를 사용 합니다.  


```no-highlight

# NFS Server 에서 설정  
[root@selinux01 ~]# semanage fcontext -a -t public_content_rw_t '/backup(/.*)?'
[root@selinux01 ~]# restorecon -vvFR /backup

[root@selinux01 ~]# ls -Z / |grep -w backup
drwxr-xr-x. root root system_u:object_r:public_content_rw_t:s0 backup
[root@selinux01 ~]#


# NFS Server 실행  
[root@selinux01 ~]# systemctl start nfs-server
[root@selinux01 ~]# systemctl status nfs-server
● nfs-server.service - NFS server and services
   Loaded: loaded (/usr/lib/systemd/system/nfs-server.service; enabled; vendor preset: disabled)
  Drop-In: /run/systemd/generator/nfs-server.service.d
           └─order-with-mounts.conf
   Active: active (exited) since Sat 2020-04-04 00:01:50 KST; 5s ago
  Process: 2230 ExecStopPost=/usr/sbin/exportfs -f (code=exited, status=0/SUCCESS)
  Process: 2227 ExecStopPost=/usr/sbin/exportfs -au (code=exited, status=0/SUCCESS)
  Process: 2226 ExecStop=/usr/sbin/rpc.nfsd 0 (code=exited, status=0/SUCCESS)
  Process: 2260 ExecStartPost=/bin/sh -c if systemctl -q is-active gssproxy; then systemctl reload gssproxy ; fi (code=exited, status=0/SUCCESS)
  Process: 2245 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
  Process: 2244 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
 Main PID: 2245 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/nfs-server.service

Apr 04 00:01:50 selinux01 systemd[1]: Starting NFS server and services...
Apr 04 00:01:50 selinux01 systemd[1]: Started NFS server and services.
[root@selinux01 ~]#



# NFS Client 에서 확인  
[root@selinux02 ~]# mount -t nfs 192.168.0.10:/backup /data
[root@selinux02 ~]# ls -Z / |grep -w data
drwxr-xr-x. root root system_u:object_r:nfs_t:s0       data
[root@selinux02 ~]#


# NFS Client context 확인  
[root@selinux02 ~]# mount -t nfs 192.168.0.10:/backup /data
[root@selinux02 ~]# cd /data
[root@selinux02 data]# ls
[root@selinux02 data]# touch 1 2
[root@selinux02 data]# ls -Z
-rw-r--r--. root root system_u:object_r:nfs_t:s0       1
-rw-r--r--. root root system_u:object_r:nfs_t:s0       2
[root@selinux02 data]#
```


## SAMBA 설정  

> samba config 부분은 생략 하였습니다.  samba selinux 설정 부분만을 설명 합니다.  
> selinux01 SAMBA Server / selinux02 SAMBA Client  



```no-highlight
# selinux01 설정 samba context 는 지정 하지 않았습니다.  
# samba 디렉토리 권한은 777 입니다.  
[root@selinux01 ~]# systemctl start smb;systemctl start nmb
[root@selinux01 ~]# ls -Z / |grep -w samba
drwxrwxrwx. root root unconfined_u:object_r:default_t:s0 samba
[root@selinux01 ~]# smbclient -L //192.168.0.10/samba -U test
Enter SAMBA\test's password:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        samba           Disk      share data
        IPC$            IPC       IPC Service (Samba 4.9.1)
        test            Disk      Home Directories
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        SAMBA                SELINUX01
[root@selinux01 ~]# #



# selinux02 ls -Z 확인시 cifs_t context 가 설정 되어 있습니다.  
selinux01 에서 samba context 를 지정 하지 않아 touch 하여 빈파일을 생성 할수 없습니다.  

[root@selinux02 /]# ls -Z / |grep -w mnt
drwxr-xr-x. root root system_u:object_r:cifs_t:s0      mnt
[root@selinux02 /]#

[root@selinux02 ~]# mount -t cifs -o user=test,password=password //192.168.0.10/samba /mnt
[root@selinux02 ~]# cd /mnt/
[root@selinux02 mnt]# touch 0
touch: cannot touch ‘0’: Permission denied
[root@selinux02 mnt]#


# selinux01 samba context 적용후 테스트
samba context 적용후에는 정상적으로 빈파일을 생성 할수 있습니다.  

[root@selinux01 ~]# semanage fcontext -a -t samba_share_t '/samba(/.*)?'
[root@selinux01 ~]# restorecon -vvFR /samba
[root@selinux02 mnt]# touch 0
[root@selinux02 mnt]# touch 1
[root@selinux02 mnt]# ls -Z
-rwxr-xr-x. root root system_u:object_r:cifs_t:s0      0
-rwxr-xr-x. root root system_u:object_r:cifs_t:s0      1
[root@selinux02 mnt]#
```