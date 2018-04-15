这篇文章主要介绍了SUSE Linux下源码编译方式安装MySQL 5.6过程分享,本文使用SUSE Linux Enterprise Server 10 SP3 (x86_64)系统,需要的朋友可以参考下.MySQL为开源数据库，因此可以基于源码实现安装。基于源码安装有更多的灵活性。也就是说我们可以针对自己的硬件平台选用合适的编译器来优化编译后的二进制代码，根据不同的软件平台环境调整相关的编译参数，选择自身需要选择不同的安装组件，设定需要的字符集等等一些可以根据特定应用场景所作的各种调整。本文描述了如何在源码方式下安装MySQL。

1. 安装环境及介质
```
SZDB:~ # cat /etc/issue
Welcome to SUSE Linux Enterprise Server 10 SP3 (x86_64) - Kernel \r (\l).
SZDB:~ # uname -a
Linux SZDB 2.6.16.60-0.54.5-smp #1 SMP Fri Sep 4 01:28:03 UTC 2009 x86_64 x86_64 x86_64 GNU/Linux
#安装介质，可以到以下网址下载安装介质，注意下载时选择source code，当前版本为5.6.17
#根据你自身的环境下载相应的安装介质，本文演示的安装介质为Generic Linux (Architecture Independent), mysql-5.6.17.tar.gz
http://dev.mysql.com/downloads/mysql
#源码安装方式官方网站链接: http://dev.mysql.com/doc/refman/5.6/en/installing-source-distribution.html
```

2. 系统需求     

Source Installation System Requirements Installation of MySQL from source requires several development tools. Some of these tools are needed no matter whether you use a standard source distribution or a development source tree. Other tool requirements depend on which installation method you use.
```
To install MySQL from source, your system must have the following tools, regardless of installation method:
CMake, which is used as the build framework on all platforms. CMake can be downloaded from http://www.cmake.org.
A good make program. Although some platforms come with their own make implementations, it is highly recommended that you use GNU make 3.75 or newer. It may already be available on your system as gmake. GNU make is available from http://www.gnu.org/software/make/.
A working ANSI C++ compiler. GCC 4.2.1 or later, Sun Studio 12 or later, Visual Studio 2010 or later, and many current vendor-supplied compilers are known to work.
Perl is needed if you intend to run test scripts. Most Unix-like systems include Perl. On Windows, you can use a version such as ActiveState Perl.
#使用源码安装上面的一些开发工具是需要的，比较重要的一个是Cmake工具，通常情况下os并没有安装，需要手动安装。
#如果没有cmake会收到这个错误提示：(-bash: cmake: command not found)
```

3. 安装cmake    

#可以到这里下载cmake，我这里下载的是2.6.4版本
#http://www.cmake.org/cmake/resources/software.html
```
SZDB:~ # cd /usr/local/src/mysql_src
SZDB:/usr/local/src/mysql_src # tar -xvf cmake-2.6.4.tar.gz
SZDB:/usr/local/src/mysql_src # cd cmake-2.6.4
SZDB:/usr/local/src/mysql_src/cmake-2.6.4 # ./bootstrap  
SZDB:/usr/local/src/mysql_src/cmake-2.6.4 # ./make
SZDB:/usr/local/src/mysql_src/cmake-2.6.4 # ./make install
```

4. 安装MySQL
```
SZDB:~ # groupadd mysql
SZDB:~ # useradd -r -g mysql mysql
SZDB:~ # cd /usr/local/src/mysql_src
SZDB:/usr/local/src/mysql_src # tar -xvf mysql-5.6.17.tar.gz 
SZDB:/usr/local/src/mysql_src # ls
mysql-5.6.17  mysql-5.6.17.tar.gz
SZDB:/usr/local/src/mysql_src # cd mysql-5.6.17/
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # cmake .
             ..........
  -- Check size of wint_t - done
  -- Could NOT find Curses  (missing:  CURSES_LIBRARY CURSES_INCLUDE_PATH)
  CMake Error at cmake/readline.cmake:85 (MESSAGE):                 Author : Leshami
    Curses library not found.  Please install appropriate package,  Blog   :http://blog.csdn.net/leshami
    remove CMakeCache.txt and rerun cmake.On Debian/Ubuntu, package name is libncurses5-dev, 
    on Redhat and derivates it is ncurses-devel.
  Call Stack (most recent call first):
    cmake/readline.cmake:128 (FIND_CURSES)
    cmake/readline.cmake:202 (MYSQL_USE_BUNDLED_EDITLINE)
    CMakeLists.txt:411 (MYSQL_CHECK_EDITLINE)
#如果cmake期间碰到上述错误，应该安装ncurses-devel rpm包，然后移除CMakeCache.txt
-- Configuring incomplete, errors occurred!
#如有没有对应的os安装光盘，可以从下面的链接下载这个rpm包，注意对应的版本号应一致
http://www.filewatcher.com/m/ncurses-devel-5.5-18.11.x86_64.rpm.735840-0.html
SZDB:/usr/local/src/mysql_src # rpm -Uvh ncurses-devel-5.5-18.11.x86_64.rpm
Preparing...                ########################################### [100%]
   1:ncurses-devel          ########################################### [100%]
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # rm -rf CMakeCache.txt
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # cmake .      #再次执行cmake
                    ........
   -- Library mysqlserver depends on OSLIBS -lpthread;m;rt;crypt;dl;aio
   -- Configuring done
   -- Generating done
   -- Build files have been written to: /usr/local/src/mysql_src/mysql-5.6.17
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # make
   Linking CXX executable mysqltest_embedded
   [100%] Built target mysqltest_embedded
   Scanning dependencies of target my_safe_process
   [100%] Building CXX object mysql-test/lib/My/SafeProcess/CMakeFiles/my_safe_process.dir/safe_process.cc.o
   Linking CXX executable my_safe_process
   [100%] Built target my_safe_process
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # make install
SZDB:/usr/local/src/mysql_src/mysql-5.6.17 # cd /usr/local/mysql
SZDB:/usr/local/mysql # chown -R mysql .
SZDB:/usr/local/mysql # chgrp -R mysql .
SZDB:/usr/local/mysql # scripts/mysql_install_db --user=mysql
                    ..........
    To start mysqld at boot time you have to copy
    support-files/mysql.server to the right place for your system
   PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
    To do so, start the server, then issue the following commands: 
      ./bin/mysqladmin -u root password 'new-password'
      ./bin/mysqladmin -u root -h SZDB password 'new-password'   
    Alternatively you can run:
     ./bin/mysql_secure_installation
 which will also give you the option of removing the test
    databases and anonymous user created by default.  This is
    strongly recommended for production servers.
    See the manual for more instructions.
    You can start the MySQL daemon with:
cd . ; ./bin/mysqld_safe &
You can test the MySQL daemon with mysql-test-run.pl
 cd mysql-test ; perl mysql-test-run.pl
New default config file was created as ./my.cnf and
will be used by default by the server when you start it.
 You may edit this file to change server settings
   ..........
SZDB:/usr/local/mysql # chown -R root .
SZDB:/usr/local/mysql # chown -R mysql data     
SZDB:/usr/local/mysql # cp support-files/mysql.server /etc/init.d/mysql.server  #配置自启动
SZDB:/usr/local/mysql # cp support-files/my-default.cnf /etc/my.cnf             #添加缺省的my.cnf配置文件  
SZDB:/usr/local/mysql # bin/mysqld_safe --user=mysql &                          #启动mysql
[1] 21004
SZDB:/usr/local/mysql # 140521 02:54:54 mysqld_safe Logging to '/usr/local/mysql/data/SZDB.err'.
140521 02:54:54 mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/data
#配置环境变量，通过软链或者修改环境变量实现(PATH=$PATH:/usr/local/mysql/bin/;export PATH)
SZDB:~ # ln -fs /usr/local/mysql/bin/mysql /usr/local/bin  
SZDB:~ # ln -fs /usr/local/mysql/bin/mysqladmin /usr/local/bin  
SZDB:~ # ln -fs /usr/local/mysql/bin/mysqld_safe /usr/local/bin 
#登陆到mysql
SZDB:/usr/local/bin # mysql -uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.17 Source distribution
Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)
```

5.
