1、准备工作
从MySQL官网上分别下载mysql服务器端于客户端包：
MySQL-server-5.5.31-1.linux2.6.x86_64.rpm
MySQL-client-5.5.31-1.linux2.6.x86_64.rpm

2、检测系统是否安装MySQL
#rpm -qa | grep -i mysql
若已安装过，会出现：
MySQL-server-5.0.22-0.i386
MySQL-client-5.0.22-0.i386
那么输入以下命令删除它：
#rpm -ev MySQL-server-5.0.22-0.i386
#rpm -ev MySQL-client-5.0.22-0.i386
注意：若删除的时候，出现删除不成功，出现依赖的包，前提必须删除依赖项。rpm -ev dovecot-1.0.7-7.el5.x86_64

3、安装MySQL
#rpm -ivh MySQL-server-5.5.31-1.linux2.6.x86_64.rpm
#rpm -ivh MySQL-client-5.5.31-1.linux2.6.x86_64.rpm

4、先了解下默认安装位置及作用
1.数据库目录      /var/lib/mysql/ 
2.配置文件   　　 /usr/share/mysql   （mysql.server命令及配置文件） 
3.相关命令        /usr/bin            (mysqladmin mysqldump等命令) 
4.启动脚本   　　 /etc/rc.d/init.d/  （启动脚本文件mysql的目录） 如：/etc/rc.d/init.d/mysql start/restart/stop/status
特别说明：SuSE Linux的启动脚本目录在  /etc/init.d 目录下，并且MySQL安装完成后，会缺少mysql.sock这个套接字文件，所以必须到这个目录下才能启动mysql

5、迁移数据库目录
由于MySQL数据库目录占用磁盘比较大，所以我在/根目录下建了个个目录data，命令如下：
#cd /
#mkdir data
#chmod 755 data
把数据库移动到data目录中去。输入以下命令：
#mv /var/lib/mysql /data
最后，进入data目录就会看到有一个mysql文件夹。
拷贝完后还需修改 /etc/init.d/mysql 的datadir目录值，修改结果如下：
basedir=
datadir=/data/mysql

6、写配置文件
拷贝配置文件到/etc目录下，并命名为my.cnf   (必须名为my.cnf)
#cp /usr/share/mysql/my-medium.cnf /etc/my.cnf
编辑 my.cnf ：
[client]
password = 123456
port = 3306
socket = /data/mysql/mysql.sock
default-character-set=utf8
[mysqld]
port = 3306
socket = /data/mysql/mysql.sock
skip-external-locking
key_buffer_size = 16M
max_allowed_packet = 1M
table_open_cache = 64
sort_buffer_size = 512K
net_buffer_length = 8K
read_buffer_size = 256K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
character_set_server=utf8
collation-server=utf8_general_ci
lower_case_table_names=1
character_set_client=utf8
max_connections=1000
[mysql]
default-character-set = utf8
no-auto-rehash

7、重启mysql服务
#cd /usr/bin
#mysql_install_db
#cd /etc/init.d
#./mysql restart

8、登录mysql
#cd /usr/bin
#mysql -u root -p
Enterpassword: (直接回车，因为第一次为空密码)
登录成功后，修改密码
mysql> show databases;
mysql> use mysql;
mysql> show tables;
mysql> update user set password=password('123456')where user='root';
最后重启mysql，密码生效
#/usr/local/mysql/bin/mysqld_safe --user=mysql &
#cd /etc/init.d
#./mysql restart

9、打开防火墙
#vi /etc/sysconfig/SuSEfirewall2
FW_SERVICES_EXT_TCP="22 5901 80 3306"
Esc 后 :wq 保存退出
重启防火墙：
#rcSuSEfirewall2 restart
检查MySQL服务是否已经启动：
#netstat -nat | grep 3306
检查设置的utf8编码是否生效：
mysql> show variables like '%colla%';
mysql> show variables like '%char%';

10、设置远程访问：
#cd /usr/bin
#mysql -u root -p                        （或者      mysql -u root -p -h 127.0.0.1       ）
mysql> use mysql; 
mysql> grant all PRIVILEGES on *.* to root@'我的IP' identified by 'password'; 
mysql> select host,user from user;
mysql> FLUSH PRIVILEGES;

11、MySQL设置自启动
chkconfig mysql on
这里因为没有套接字，所以不一定会成功。

12、MySQL导出导入
mysql> mysqldump abc(数据库名) --user=root --password > /root/abc.sql      #导出整个库
mysql> mysqldump -u root -p abc(数据库名) user （表名）> /user.sql         #导出单张表
注意，如果运行 mysqldump 没有指定 --quick 或 --opt 选项，则会将整个结果集放在内存中。如果导出大数据库的话可能会出现问题
导入：
mysql> CREATE DATABASE abc;
mysql> use abc;
mysql> source /root/abc.sql    #导入数据库
mysql> source /user.sql        #导入单张表
转载：http://xinchibaobei.blog.163.com/blog/static/12639250920134954835786/
