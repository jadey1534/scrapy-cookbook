.. _header-n0:

zabbix4.4搭建
=============

.. _header-n2:

环境要求
--------

服务器要求：

-  centos7

   .. code:: shell

      [root@localhost ~]# cat /etc/centos-release
      CentOS Linux release 7.3.1611 (Core)

-  2G内存

-  双核CPU

-  硬盘空间30-50G

-  zabbix 4.4

-  mariadb数据库

-  ngnix服务

.. _header-n20:

安装步骤
--------

.. _header-n21:

安装mariadb数据库
~~~~~~~~~~~~~~~~~

更新yum仓库

.. code:: shell

   yum -y update

删除自带数据库

.. code:: shell

   [root@localhost ~]# rpm -qa|grep db
   man-db-2.6.3-9.el7.x86_64
   libdb-5.3.21-19.el7.x86_64
   dbus-libs-1.6.12-17.el7.x86_64
   gdbm-1.10-8.el7.x86_64
   dbus-glib-0.100-7.el7.x86_64
   libdb-utils-5.3.21-19.el7.x86_64
   dbus-python-1.1.1-9.el7.x86_64
   python-slip-dbus-0.4.0-2.el7.noarch
   mariadb-libs-5.5.52-1.el7.x86_64
   dbus-1.6.12-17.el7.x86_64
   [root@localhost ~]# yum remove mariadb-libs-5.5.52-1.el7.x86_64

添加yum源

.. code:: shell

   vi /etc/yum.repos.d/MariaDB.repo

.. code:: shell

   [mariadb]
   name = MariaDB
   baseurl = http://yum.mariadb.org/10.2/centos7-amd64
   gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
   gpgcheck=1

.. code:: 

   [mariadb]
   name = MariaDB
   baseurl = https://mirrors.ustc.edu.cn/mariadb/yum/10.5/centos7-amd64/
   gpgkey=https://mirrors.ustc.edu.cn/mariadb/yum/RPM-GPG-KEY-MariaDB
   gpgcheck=1

安装mariadb

.. code:: shell

   yum install MariaDB-server MariaDB-client -y

启动数据库服务

.. code:: shell

   systemctl start mariadb

对数据库进行安全配置

.. code:: shell

   [root@localhost ~]# mysql_secure_installation 

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

开机启动

.. code:: shell

   systemctl enable mariadb

查询版本

.. code:: shell

   mysql --version

.. _header-n40:

安装nginx
~~~~~~~~~

新建nginx yum文件

.. code:: shell

   vi /etc/yum.repos.d/nginx.repo

输入下面信息

.. code:: shell

   [nginx-stable]
   name=nginx stable repo
   baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
   gpgcheck=1
   enabled=1
   gpgkey=https://nginx.org/keys/nginx_signing.key

   [nginx-mainline]
   name=nginx mainline repo
   baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=https://nginx.org/keys/nginx_signing.key

查看是否添加成功

.. code:: shell

   [root@localhost ~]# yum search nginx
   Loaded plugins: fastestmirror
   nginx-stable                                                                                | 2.9 kB  00:00:00     
   nginx-stable/7/x86_64/primary_db                                                            |  55 kB  00:00:00     
   Loading mirror speeds from cached hostfile
    * base: mirrors.tuna.tsinghua.edu.cn
    * extras: mirrors.tuna.tsinghua.edu.cn
    * updates: mirrors.tuna.tsinghua.edu.cn
   =============================================== N/S matched: nginx ================================================
   nginx-debug.x86_64 : debug version of nginx
   nginx-debuginfo.x86_64 : Debug information for package nginx
   nginx-module-geoip.x86_64 : nginx GeoIP dynamic modules
   nginx-module-geoip-debuginfo.x86_64 : Debug information for package nginx-module-geoip
   nginx-module-image-filter.x86_64 : nginx image filter dynamic module
   nginx-module-image-filter-debuginfo.x86_64 : Debug information for package nginx-module-image-filter
   nginx-module-njs.x86_64 : nginx njs dynamic modules
   nginx-module-njs-debuginfo.x86_64 : Debug information for package nginx-module-njs
   nginx-module-perl.x86_64 : nginx Perl dynamic module
   nginx-module-perl-debuginfo.x86_64 : Debug information for package nginx-module-perl
   nginx-module-xslt.x86_64 : nginx xslt dynamic module
   nginx-module-xslt-debuginfo.x86_64 : Debug information for package nginx-module-xslt
   nginx-nr-agent.noarch : New Relic agent for NGINX and NGINX Plus
   pcp-pmda-nginx.x86_64 : Performance Co-Pilot (PCP) metrics for the Nginx Webserver
   nginx.x86_64 : High performance web server

     Name and summary matches only, use "search all" for everything.

安装nginx

.. code:: shell

   yum install nginx

确认安装完成

.. code:: shell

   rpm -qa|grep nginx

启动并添加开机自启动

.. code:: shell

   systemctl start nginx
   systemctl enable nginx

查看状态

.. code:: shell

   systemctl status nginx

查看端口监听情况

.. code:: shell

   ss -tnl|grep 80

开放80端口

.. code:: shell

   firewall-cmd  --permanent --add-port=80/tcp
   firewall-cmd --reload

.. _header-n59:

安装php7.0 
~~~~~~~~~~~

安装php7的yum源

.. code:: shell

   rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

查看是否添成功

.. code:: shell

   [root@localhost ~]# yum search php
   ····
   php71w-opcache.x86_64 : An opcode cache Zend extension
   php71w-pecl-apcu.x86_64 : APCu - APC User Cache
   php71w-pecl-apcu-devel.x86_64 : APCu developer files (header)
   php71w-pecl-geoip.x86_64 : Extension to map IP addresses to geographic places
   php71w-pecl-igbinary-devel.x86_64 : Igbinary developer files (header)
   php71w-pecl-imagick.x86_64 : Provides a wrapper to the ImageMagick library
   php71w-pecl-imagick-devel.x86_64 : Imagick developer files (header)
   php71w-pecl-memcached.x86_64 : Extension to work with the Memcached caching daemon
   php71w-pecl-mongodb.x86_64 : PECL package MongoDB driver
   php71w-pecl-redis.x86_64 : Extension for communicating with the Redis key-value store
   php72w-opcache.x86_64 : An opcode cache Zend extension
   php72w-pecl-apcu.x86_64 : APCu - APC User Cache
   php72w-pecl-apcu-devel.x86_64 : APCu developer files (header)
   php72w-pecl-geoip.x86_64 : Extension to map IP addresses to geographic places
   php72w-pecl-igbinary-devel.x86_64 : Igbinary developer files (header)
   php72w-pecl-imagick.x86_64 : Provides a wrapper to the ImageMagick library
   php72w-pecl-imagick-devel.x86_64 : Imagick developer files (header)
   php72w-pecl-libsodium.x86_64 : Wrapper for the Sodium cryptographic library
   php72w-pecl-memcached.x86_64 : Extension to work with the Memcached caching daemon
   php72w-pecl-mongodb.x86_64 : PECL package MongoDB driver
   php72w-pecl-redis.x86_64 : Extension for communicating with the Redis key-value store
   php72w-sodium.x86_64 : Wrapper for the Sodium cryptographic library
   ····

..

   省略部分输出

安装php和php-fpm

.. code:: shell

   yum install -y php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64 php70w-mysql.x86_64 php70w-pdo.x86_64
   yum install -y php70w-fpm php70w-opcache

查看是否安装成功

.. code:: shell

   [root@localhost ~]# php -v
   PHP 7.0.33 (cli) (built: Dec  6 2018 22:30:44) ( NTS )
   Copyright (c) 1997-2017 The PHP Group
   Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
       with Zend OPcache v7.0.33, Copyright (c) 1999-2017, by Zend Technologies

查看php扩展

.. code:: shell

   [root@localhost ~]# php -m
   [PHP Modules]
   bz2
   calendar
   Core
   ctype
   curl
   date
   exif
   fileinfo
   filter
   ftp
   gd
   gettext
   gmp
   hash
   iconv
   json
   ldap
   libxml
   mbstring
   mcrypt
   mysqli
   openssl
   pcntl
   pcre
   PDO
   pdo_mysql
   pdo_sqlite
   Phar
   readline
   Reflection
   session
   shmop
   SimpleXML
   sockets
   SPL
   sqlite3
   standard
   tokenizer
   xml
   Zend OPcache
   zip
   zlib

   [Zend Modules]
   Zend OPcache

..

   需要安装其他扩展，通过 yum install php70w-XXX(扩展名字)

启动并开机启动php-fpm

.. code:: shell

   systemctl start php-fpm
   systemctl enable php-fpm

.. _header-n76:

安装zabbix4.4 
~~~~~~~~~~~~~~

安装zabbix yum源

.. code:: shell

   rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm
   yum clean all

安装zabbix-server和agent

.. code:: shell

   yum install -y zabbix-server-mysql zabbix-agent

安装epel资料库和zabbix前端软件包

.. code:: shell

   yum install epel-release

   yum install zabbix-web-mysql zabbix-nginx-conf

mariadb创建zabbix账号密码（zabbix，下面导入数据库输入这个密码）以及授权zabbix数据库

.. code:: shell

   #mysql -uroot -p
   password
   mysql> create database zabbix character set utf8 collate utf8_bin;
   mysql> create user zabbix@localhost identified by 'zabbix';
   mysql> grant all privileges on zabbix.* to zabbix@localhost;
   mysql> quit;

导入初始架构和数据

.. code:: shell

   zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix

修改zabbix_server配置数据库

.. code:: shell

   vi /etc/zabbix/zabbix_server.conf
   ```shell
       ········
       DBPassword=zabbix
       ········

..

   配置行号124

配置nginx，删除默认文件

.. code:: shell

   mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.bak

编辑/etc/nginx/conf.d/zabbix.conf，取消以下两行注释

.. code:: shell

   # listen 88;
   # server_name example.com;

配置php，修改/etc/php-fpm.d/zabbix.conf,取消注释，修改时区

.. code:: shell

   php_value[date.timezone] = Asia/Shanghai

临时关闭selinux

.. code:: shell

   setenforce 0

永久关闭selinux,编辑/etc/selinux/config,修改

.. code:: shell

   SELINUX=enforcing  ----->  SELINUX=disable

重启进程

.. code:: shell

   systemctl restart zabbix-server zabbix-agent nginx php-fpm
   systemctl enable zabbix-server zabbix-agent 

| 导入字体包，zabbix默认情况下中文显示图形界面显示会出现乱码，需要导入字体包
| 电脑打开：C:\Windows\Fonts，找到\ **华文行楷**\ ，复制上传到服务器/usr/share/zabbix/assets/fonts目录下

   可以按照个人喜好上传字体

.. code:: shell

   [root@localhost fonts]# ls -l 
   total 3924
   lrwxrwxrwx. 1 root root      33 Jul 23 21:26 graphfont.ttf -> /etc/alternatives/zabbix-web-font
   -rw-r--r--. 1 root root 4016288 Jul  7  2019 stxingka.ttf
   [root@localhost fonts]# pwd
   /usr/share/zabbix/assets/fonts

编辑/usr/share/zabbix/include/defines.inc.php，将69和111行graphfont修改为stxingka

.. code:: shell

   [root@localhost fonts]# cat /usr/share/zabbix/include/defines.inc.php|grep -n stxingka
   69:define('ZBX_GRAPH_FONT_NAME',                'stxingka'); // font file name
   111:define('ZBX_FONT_NAME', 'stxingka');

重启服务

.. code:: shell

   systemctl restart zabbix-server

web打开服务器IP地址，按照提示输入信息即可，安装后登录，账号密码默认：Admin/zabbix

.. figure:: C:\Users\LHOS\AppData\Roaming\Typora\typora-user-images\image-20220413182626830.png
   :alt: 
