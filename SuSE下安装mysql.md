Open SUSE12.2 MySQL5.6数据库安装配置

 

系统环境：Open SUSE12.2 x86_64

 

一、下载"SuSE Linux Enterprise Server 11 (x86， 64-bit)， RPM Bundle" 或者"SuSE Linux Enterprise Server 11 (x86， 64-bit)， RPM Package Client Utilities"、"SuSE Linux Enterprise Server 11 (x86， 64-bit)， RPM PackageMySQL Server"，地址：https://dev.mysql.com/downloads/mysql/

 

二、因为在新安装完的SUSE 12.2系统中，原本已有MySQL5.5.25a版本的MySQL，建议清除（本次安装是已清除原有Mysql5.5.25版本后的安装过程），不过也可以在安装过程中覆盖它。按照MySQL官网文档叙述，如果需要在本地登录，则需安同时装MySQL-server和MySQL-client：

rpm -ivh MySQL-server-5.6.13-1.sles11.x86_64.rpm

rpm -ivh MySQL-client-5.6.13-1.sles11.x86_64.rpm

 

三、安装完成后，你可以在目录/usr下找到my.cnf，不过这个my.cnf没什么内容，下面配置一下my.cnf，该配置仅作参考使用。

[plain] 
# The following options will be passed to all MySQL clients  
[client]  
#password   = your_password  
port        = 3306  
socket      = /opt/mysql/mysql.sock  
 
# Here follows entries for some specific programs  
# The MySQL server  
[mysqld]  
port        = 3306  
socket      = /opt/mysql/mysql.sock  
# Change following line if you want to store your database elsewhere  
basedir=/usr  
datadir = /opt/mysql  
skip-external-locking  
key_buffer_size = 16M  
max_allowed_packet = 1M  
table_open_cache = 64  
sort_buffer_size = 512K  
net_buffer_length = 8K  
read_buffer_size = 256K  
read_rnd_buffer_size = 512K  
myisam_sort_buffer_size = 8M  
lower_case_table_names=1  
 


# Don't listen on a TCP/IP port at all. This can be a security enhancement， 
# if all processes that need to connect to mysqld run on the same host. 
# All interaction with mysqld must be made via Unix sockets or named pipes. 
# Note that using this option without enabling named pipes on Windows 
# (via the "enable-named-pipe" option) will render mysqld useless!  

 

#   
#skip-networking  
# Replication Master Server (default)  
# binary logging is required for replication  
log-bin=mysql-bin  
# binary logging format - mixed recommended  
binlog_format=mixed  
​
# required unique id between 1 and 2^32 - 1  
# defaults to 1 if master-host is not set  
# but will not function as a master if omitted  
server-id   = 1  
# Replication Slave (comment out master section to use this)  
#  
# To configure this host as a replication slave， you can choose between  
# two methods :  
#  
# 1) Use the CHANGE MASTER TO command (fully described in our manual) -  
#    the syntax is:  
#  
#    CHANGE MASTER TO MASTER_HOST=<host>， MASTER_PORT=<port>，  
#    MASTER_USER=<user>， MASTER_PASSWORD=<password> ;  
#  
#    where you replace <host>， <user>， <password> by quoted strings and  
#    <port> by the master's port number (3306 by default).  
#  
#    Example:  
#  
#    CHANGE MASTER TO MASTER_HOST='125.564.12.1'， MASTER_PORT=3306，  
#    MASTER_USER='joe'， MASTER_PASSWORD='secret';  
#  
# OR  
#  
 

# 2) Set the variables below. However， in case you choose this method， then 
#    start replication for the first time (even unsuccessfully， for example 
#    if you mistyped the password in master-password and the slave fails to 
#    connect)， the slave will create a master.info file， and any later 
#    change in this file to the variables' values below will be ignored and 
#    overridden by the content of the master.info file， unless you shutdown 
#    the slave server， delete master.info and restart the slaver server. 
#    For that reason， you may want to leave the lines below untouched 
#    (commented) and instead use CHANGE MASTER TO (see above) 
#  
# required unique id between 2 and 2^32 - 1  
# (and different from the master)  
# defaults to 2 if master-host is set  
# but will not function as a slave if omitted  
#server-id       = 2  
#  
# The replication master for this slave - required  
#master-host     =   <hostname>  
#  
# The username the slave will use for authentication when connecting  
# to the master - required  
#master-user     =   <username>  
#  
# The password the slave will authenticate with when connecting to  
# the master - required  
#master-password =   <password>  
#  
# The port the master is listening on.  
# optional - defaults to 3306  
#master-port     =  <port>  
#  
# binary logging - not required for slaves， but recommended  
#log-bin=mysql-bin  
# Uncomment the following if you are using InnoDB tables  
innodb_data_home_dir = /opt/mysql  
innodb_data_file_path = ibdata1:10M:autoextend  
innodb_log_group_home_dir = /opt/mysql  
# You can set .._buffer_pool_size up to 50 - 80 %  
# of RAM but beware of setting memory usage too high  
innodb_buffer_pool_size = 16M  
#innodb_additional_mem_pool_size = 2M  
# Set .._log_file_size to 25 % of buffer pool size  
innodb_log_file_size = 5M  
innodb_log_buffer_size = 8M  
innodb_flush_log_at_trx_commit = 1  
#innodb_lock_wait_timeout = 50  
# The safe_mysqld script  
[safe_mysqld]  
log-error   = /var/log/mysql/mysqld.log  
socket      = /opt/mysql/mysql.sock  
[mysqldump]  
socket      = /opt/mysql/mysql.sock  
quick  
max_allowed_packet = 16M  
[mysql]  
no-auto-rehash  
# Remove the next comment character if you are not familiar with SQL  
#safe-updates  
[myisamchk]  
key_buffer_size = 20M  
sort_buffer_size = 20M  
read_buffer = 2M  
write_buffer = 2M  
[mysqlhotcopy]  
interactive-timeout  
[mysqld_multi]  
mysqld     = /usr/bin/mysqld_safe  
mysqladmin = /usr/bin/mysqladmin  
log        = /var/log/mysqld_multi.log

 

四、从给出的配置文件可以看到几个信息与默认的不一样

1、basedir路径的配置

2、datadir路径的配置

3、[client] socket路径的配置

4、[mysqld] socket路径的配置

5、[safe_mysqld] socket路径的配置

6、[mysqldump] socket路径的配置

 

由于datadir路径配置为“/opt/mysql”，则需要把默认路径"/var/lib"下整个"mysql"文件夹复制到"/opt"下。以下是需要特别注意的地方：先查看一下"/var/lib"下"mysql"的权限和所有者；发现该文件夹的所有者为mysql。再查看"/opt"下刚才复制的"mysql"文件夹所有者，发现该所有者是root，此时如果启动mysql服务，会报错误：StartingMySQL.The server quit without updating PID file。修改"/opt"目录下的"mysql"文件夹所有者：

/opt>chown -R mysql:mysql mysql

 

注意：[client] socket路径的配置、[mysqld] socket路径的配置、[safe_mysqld] socket路径的配置、[mysqldump] socket路径的配置，都是配置在"/opt/mysql"下。否则会报没有找到mysql.sock错误。

启动mysql服务：service mysql start

查看mysql服务状态：service mysql status

发现一切正常，利用安装时随机生成的root密码（在`/.mysql_secret中）登陆，修改root密码或添加新用户。
