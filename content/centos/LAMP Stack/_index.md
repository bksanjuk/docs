+++
title = "LAMP Stack"
weight = 24
+++


## LAMP Stack  

{{% notice note %}}
What is LAMP Stack  
LAMP Stack 은 웹 사이트 및 웹 응용 프로그램을 만드는 데 사용할 수있는 일련의 오픈 소스 소프트웨어입니다. LAMP는 머리 글자로, 일반적으로 Linux 운영 체제,  
Apache HTTP Server, MySQL 관계형 데이터베이스 관리 시스템 및 PHP 프로그래밍 언어로 구성됩니다.  
{{% /notice %}}

{{% notice info %}}
설치 순서 참고  
본 문서의 경우 mysql 이 아닌 mariadb 로 설치를 진행하며 설치 순서는 mariadb -> php -> apache 순으로 진행 합니다.  
{{% /notice %}}

* **CentOS 7 설치**  

[Mariadb repo확인](https://downloads.mariadb.org/mariadb/repositories/#mirror=kaist&distro=CentOS&distro_release=centos7-amd64–centos7&version=10.2)  

* mariadb repo 생성  
```no-highlight
[root@lamp ~]# vi /etc/yum.repos.d/mariadb.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
[root@lamp ~]# yum clean all && yum list
```


* mariadb 10.1 설치  
```no-highlight
[root@lamp ~]# yum install -y mariadb mariadb-server
```

* mariadb 실행 및 daemon 활성화  
```no-highlight
[root@lamp ~]# systemctl start mariadb.service
[root@lamp ~]# systemctl enable mariadb.service
```

* mariadb secure_installation 실행  (root 패스워드 설정)
```no-highlight
[root@lamp ~]# /usr/bin/mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
[root@lamp ~]#
```

* mariadb status 확인  
characterset 의 경우 차후 변경 합니다.  
```no-highlight
[root@lamp ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 11
Server version: 10.1.36-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> status;
--------------
mysql  Ver 15.1 Distrib 10.1.36-MariaDB, for Linux (x86_64) using readline 5.1

Connection id:          11
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server:                 MariaDB
Server version:         10.1.36-MariaDB MariaDB Server
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
UNIX socket:            /var/lib/mysql/mysql.sock
Uptime:                 1 min 48 sec

Threads: 1  Questions: 23  Slow queries: 0  Opens: 18  Flush tables: 1  Open tables: 12  Queries per second avg: 0.212
--------------

MariaDB [(none)]> quit
Bye
[root@lamp ~]#
```


* mariadb characterset 변경 및 확인  
```no-highlight
[root@lamp ~]# vi /etc/my.cnf.d/server.cnf
[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[root@lamp ~]# vi /etc/my.cnf.d/client.cnf
[client]

default-character-set = utf8mb4

[root@lamp ~]# systemctl restart mariadb.service
[root@lamp ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 10.1.36-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> status;
--------------
mysql  Ver 15.1 Distrib 10.1.36-MariaDB, for Linux (x86_64) using readline 5.1

Connection id:          2
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server:                 MariaDB
Server version:         10.1.36-MariaDB MariaDB Server
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
UNIX socket:            /var/lib/mysql/mysql.sock
Uptime:                 15 sec

Threads: 1  Questions: 4  Slow queries: 0  Opens: 17  Flush tables: 1  Open tables: 11  Queries per second avg: 0.266
--------------

MariaDB [(none)]>
```




* Apache 설치  
```no-highlight
[root@lamp ~]# yum install -y httpd
```


* apache 실행 및 daemon 활성화  
```no-highlight
[root@lamp ~]# systemctl start httpd
[root@lamp ~]# systemctl enable httpd
```

* 방화벽 open  
```no-highlight
[root@lamp ~]# firewall-cmd --permanent --add-port=80/tcp
[root@lamp ~]# firewall-cmd --permanent --add-port=443/tcp
[root@lamp ~]# firewall-cmd --reload
```


{{% notice tip %}}
yum-config-manager
yum-config-manager 가 없는 경우 yum-utils 패키지를 설치 합니다.  
{{% /notice %}}


* php7 설치  

```no-highlight
[root@lamp ~]# yum install -y epel-release
[root@lamp ~]# rpm -Uvh http://ftp.riken.jp/Linux/remi/enterprise/remi-release-7.rpm
[root@lamp ~]# yum clean all && yum list
[root@lamp ~]# yum update -y
[root@lamp ~]# init 6
[root@lamp ~]# yum-config-manager --enable remi-php71
[root@lamp ~]# yum -y install php php-opcache php-mysql php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-soap curl curl-devel
```
* php.ini 설정  
```no-highlight
[root@lamp ~]# vi /etc/php.ini
;display_errors = Off
display_errors = On

; display_startup_errors = Off
display_startup_errors = On

date.timezone = Asia/Seoul

;cgi.fix_pathinfo=1
cgi.fix_pathinfo=0

```

* php info 확인  

```no-highlight
[root@lamp ~]# vi /var/www/html/info.php
<?php phpinfo(); ?>
```

* web-page 확인  

![C1](/linux/linux_tmp/config/C1.png)  


* Apache MPM 설정  

> apache 확인  
```no-highlight
[root@lamp ~]# httpd -V |grep "^Server MPM"
Server MPM:     prefork
```

* Worker 로 변경  

```no-highlight
[root@lamp ~]# vi /etc/httpd/conf.modules.d/00-mpm.conf
#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so

LoadModule mpm_worker_module modules/mod_mpm_worker.so

[root@lamp ~]# vi /etc/httpd/conf.modules.d/10-worker.conf
<IfModule mpm_worker_module>
    ServerLimit             250
    StartServers             10
    MinSpareThreads          75
    MaxSpareThreads         250
    ThreadLimit              64
    ThreadsPerChild          32
    MaxClients             8000
    MaxRequestsPerChild   10000
</IfModule>

[root@lamp ~]# systemctl restart httpd
[root@lamp ~]# httpd -V |grep "^Server MPM"
Server MPM:     worker
[root@lamp ~]#
```

* httpd.conf 설정  

```no-highlight
[root@lamp ~]# vi /etc/httpd/conf/httpd.conf
ServerRoot "/etc/httpd"   / apache root 디렉토리
 
Listen 80                 / apache Port 
 
Include conf.modules.d/*.conf  /  apache module 디렉토리
 
User apache                    / apache 실행유저
Group apache                   / apache 실행구룹
 
 
ServerAdmin root@localhost     / Server 관리자 e-mail
 
ServerName www.test.com:80  / Apache Server Domain:port
 
<IfModule dir_module>
    DirectoryIndex index.html index.php /Directory index 설정 
</IfModule>
```

* **UserDir설정**  
/home/$User/public_html 사용시 설정 합니다.  

```no-highlight
[root@lamp ~]# vi /etc/httpd/conf.d/userdir.conf
<IfModule mod_userdir.c>
    #
    # UserDir is disabled by default since it can confirm the presence
    # of a username on the system (depending on home directory
    # permissions).
    #
    #UserDir disabled

    #
    # To enable requests to /~user/ to serve the user's public_html
    # directory, remove the "UserDir disabled" line above, and uncomment
    # the following line instead:
    #
    UserDir public_html
</IfModule>



<Directory "/home/*/public_html">
        Options Indexes Includes FollowSymLinks
        Require all granted
</Directory>

```

{{% notice tip %}}
UserDir 사용시 참고사항  
/home 디렉토리의 User 디렉토리 권한은 700 입니다. 711로 권한 변경이 필요 합니다.  
{{% /notice %}}


* UserDir 확인  
```no-highlight
[root@lamp ~]# chmod 711 /home/test
[root@lamp ~]# mkdir /home/test/public_html
[root@lamp ~]# chown test:test /home/test/public_html/
[root@lamp ~]# systemctl restart httpd
[root@lamp ~]# vi /home/test/public_html/index.php
<?php phpinfo(); ?>

[root@lamp ~]# curl http://192.168.0.20/~test/
```

* VirtualHost 설정  
vhost.conf 파일이 별도로 없음으로 생성 합니다.  

```no-highlight
[root@lamp ~]# cd /etc/httpd/conf.d/
[root@lamp conf.d]# vi vhost.conf
#NameVirtualHost *:80
<VirtualHost *:80>
       ServerAdmin admin@test.com
       DocumentRoot /var/www/html/www.test.com/public_html/
       ServerName test.com
       ServerAlias www.test.com
       ErrorLog /var/www/html/www.test.com/logs/test.com-error_log
       CustomLog /var/www/html/www.test.com/logs/test.com-access_log common
</VirtualHost>
<VirtualHost *:80>
       ServerAdmin admin@test.com
       DocumentRoot /var/www/html/bbs.test.com/public_html/
       ServerName bbs.test.com
       ErrorLog /var/www/html/bbs.test.com/logs/test.com-error_log
       CustomLog /var/www/html/bbs.test.com/logs/test.com-access_log common
</VirtualHost>
```

* 디렉토리 생성 및 권한 설정  

```no-highlight
[root@lamp ~]# mkdir -p /var/www/html/www.test.com/{public_html,logs}
[root@lamp ~]# mkdir -p /var/www/html/bbs.test.com/{public_html,logs}

[root@lamp ~]# chown -R apache:apache /var/www/html/www.test.com
[root@lamp ~]# chown -R apache:apache /var/www/html/bbs.test.com

적당히 html 문서를 만들어 확인을 합니다. 
[root@lamp ~]# vi /var/www/html/www.test.com/public_html/index.html
[root@lamp ~]# vi /var/www/html/bbs.test.com/public_html/index.html
[root@lamp ~]# systemctl restart httpd
```

* 확인  

```no-highlight
[root@lamp ~]# curl http://www.test.com
<html> </html>
<h> www.test.com </h>
[root@lamp ~]# curl http://bbs.test.com
<html> </html>
<h> bbs.test.com </h>
[root@lamp ~]#
```

{{% notice info %}}
별도의 디렉토리 사용시 httpd.conf 수정하여 Directory 설정을 추가 해야 합니다.  
[apache access](http://httpd.apache.org/docs/2.4/upgrading.html#access) 에서 자세한 정보를 확인 할수 있습니다.  
{{% /notice %}}


```no-highlight
[root@lamp ~]# vi /etc/httpd/conf/httpd.conf

<Directory "/www-data/public_html">
    AllowOverride None
    Require all granted
</Directory>



[root@lamp ~]# mkdir -p /www-data/{public_html,logs}
[root@lamp ~]# chown -R apache:apache /www-data/


[root@lamp ~]# vi /etc/httpd/conf.d/vhost.conf


<VirtualHost *:80>
       ServerAdmin admin@test.com
       DocumentRoot /www-data/public_html/
       ServerName blog.test.com
       ErrorLog /www-data/logs/blog.test.com-error_log
       CustomLog /www-data/logs/blog.test.com-access_log common
</VirtualHost>


[root@lamp ~]# systemctl restart httpd
```

<br></br>