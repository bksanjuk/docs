+++
title = "LEMP Stack"
weight = 25
+++

## LEMP Stack  

{{% notice note %}}
What is LEMP Stack  
LAMP Stack 의 변형 이며 Apache 대신 Nginx 로 구성 하는것을 말합니다.  
Linux , Nginx , Mysql , php 로 구성 하지만 본 문서에서는 Mysql 대신 mariadb 를 사용 합니다.  
{{% /notice %}}

* system update  
Nginx 설치전 system update 를 진행 합니다.  
```no-highlight
[root@lemp ~]# yum update -y
```

* Nginx repo 생성 및 yum list 확인  
```no-highlight
[root@lemp ~]# vi /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1

[root@lemp ~]# yum clean all && yum list
```


* Nginx 설치  
```no-highlight
[root@lemp ~]# yum install -y nginx
```


* Mariadb repo 생성 및 yum list 확인  
```no-highlight
[root@lemp ~]# vi /etc/yum.repos.d/mariadb.repo
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

[root@lemp ~]# yum clean all && yum list
```

* Mariadb 10.1 설치  
```no-highlight
[root@lemp ~]# yum install -y mariadb mariadb-server
```

* mariadb 실행 및 Daemon 활성화  
```no-highlight
[root@lemp ~]# systemctl start mariadb.service
[root@lemp ~]# systemctl enable mariadb.service
```

* mariadb secure_installation 실행  
root 패스워드를 설정 합니다.  
```no-highlight
[root@lemp ~]# /usr/bin/mysql_secure_installation

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
[root@lemp ~]#
```

* php 설치  
```no-highlight
[root@lemp ~]# yum install -y epel-release yum-utils
[root@lemp ~]# rpm -Uvh http://ftp.riken.jp/Linux/remi/enterprise/remi-release-7.rpm
[root@lemp ~]# yum clean all && yum list
[root@lemp ~]# yum update -y
[root@lemp ~]# init 6
[root@lemp ~]# yum-config-manager --enable remi-php71
[root@lemp ~]# yum -y install php php-mysql php-fpm php-opcache php-gd php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-soap curl curl-devel
```

* Nginx 실행 및 Daemon 활성화  
```no-highlight
[root@lemp ~]# systemctl start nginx
[root@lemp ~]# systemctl enable nginx
```

* 방화벽 Open  
```no-highlight
[root@lemp ~]# firewall-cmd --permanent --add-port=80/tcp
[root@lemp ~]# firewall-cmd --permanent --add-port=443/tcp
[root@lemp ~]# firewall-cmd --reload
```

* Mariadb 설정  

```no-highlight
[root@lemp ~]# vi /etc/my.cnf.d/server.cnf
[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[root@lemp ~]# vi /etc/my.cnf.d/client.cnf
[client]

default-character-set = utf8mb4

[root@lemp ~]# systemctl restart mariadb.service
[root@lemp ~]# mysql -uroot -p
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
Uptime:                 8 sec

Threads: 1  Questions: 4  Slow queries: 0  Opens: 17  Flush tables: 1  Open tables: 11  Queries per second avg: 0.500
--------------

MariaDB [(none)]>

```

* php.ini 설정  
```no-highlight
[root@lemp ~]# vi /etc/php.ini
;display_errors = Off
display_errors = On

; display_startup_errors = Off
display_startup_errors = On

date.timezone = Asia/Seoul

;cgi.fix_pathinfo=1
cgi.fix_pathinfo=0
```

* Nginx 설정  
nginx.conf  파일에 /etc/nginx/sites-enabled 라인을 추가 합니다.  
```no-highlight
[root@lemp ~]# mkdir /etc/nginx/sites-enabled
[root@lemp ~]# vi /etc/nginx/nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*.conf;
}

```

* Nginx default.conf  
```no-highlight
server {
    listen       80;
    server_name  localhost;

    charset UTF-8;

        root   /usr/share/nginx/html;
        index  index.php index.html index.htm;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        #fastcgi_pass unix:/run/php-fpm/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

}

```

{{% notice tip %}}
php-fpm 설정  
sock 방식을 사용할 경우 fastcgi_pass unix:/run/php-fpm/php-fpm.sock; 주석을 해제 합니다.  
 www.conf 파일 을  listen = /run/php-fpm/php-fpm.sock; 로 listen = 127.0.0.1:9000 로 수정 해야 합니다.  
{{% /notice %}}


* php-fpm 설정  
```no-highlight
[root@lemp ~]# vi /etc/php-fpm.d/www.conf
user = nginx
group = nginx

listen.owner = nginx
listen.group = nginx
listen.mode = 0660
```

* php-fpm 실행 및 Daemon 활성화  
```no-highlight
[root@lemp ~]# systemctl start php-fpm.service
[root@lemp ~]# systemctl enable php-fpm.service
[root@lemp ~]# systemctl restart nginx
```


* phpinfo 확인  

```no-highlight
[root@lemp ~]# vi /usr/share/nginx/html/info.php
<?php phpinfo(); ?>
[root@lemp ~]# curl http://localhost/info.php
```

* Nginx VirtualHost 설정  

```no-highlight
[root@lemp ~]# vi /etc/nginx/conf.d/default.conf
server {
    listen       80 default_server;
    server_name  localhost;

```
* test_com.conf 파일 생성  

```no-highlight
[root@lemp ~]# mkdir -p /var/www/html/test.com/{public_html,logs}
[root@lemp ~]# vi /etc/nginx/sites-enabled/test_com.conf
server {
    listen       80;
    server_name  www.test.com test.com;
    root   /var/www/html/test.com/public_html;
    index  index.php index.html index.htm;
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        autoindex on;
    }

    access_log  /var/www/html/test.com/logs/access.log;
    error_log  /var/www/html/test.com/logs/error.log warn;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

[root@lemp ~]# chown -R nginx:nginx /var/www/html/test.com/
[root@lemp ~]# systemctl restart nginx
```
<br></br>