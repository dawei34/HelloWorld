##### Open SUSE12.2 MySQL5.6数据库安装配置
`系统环境：Open SUSE12.2 x86_64`
1. 安装前准备     
```
下载适配SuSE12的mysql5.6版本的rpm安装包     
地址：https://dev.mysql.com/downloads/mysql/
```

2.检查系统中是否存在存在未安装的mysql     
```
检查之前是否有安装过，若有删除
# rpm -qa | grep -i mysql   --该命令执行后若出现MySQL-*，表明安装过mysql，需卸载

# rpm -e         
 --卸载已安装，卸载过程中可能会报包依赖问题，则需先卸载依赖包
	rpm -e MySQL-test-5.6.32-1.sles11.x86_64
	rpm -e MySQL-embedded-5.6.32-1.sles11.x86_64
	rpm -e MySQL-devel-5.6.32-1.sles11.x86_64   
	rpm -e MySQL-shared-5.6.32-1.sles11.x86_64
	rpm -e MySQL-shared-compat-5.6.32-1.sles11.x86_64
	rpm -e MySQL-client-5.6.32-1.sles11.x86_64
	rpm -e MySQL-server-5.6.32-1.sles11.x86_64
```

3. 安装mysql     
```
# rpm -ivh MySQL-*
```

4. 启动mysql服务
```
# service mysql start
```

5. 登录
```
安装完成后，会随机生成root密码，在root文件夹下     
2018-04-16 22:06:27 17536 [Note] InnoDB: 5.6.32 started; log sequence number 0
A random root password has been set. You will find it in '/root/.mysql_secret'.     
修改密码有如下几种方式
#1 mysqladmin -u root -p password "123456"
Enter password: [输入原来的密码]
#2 mysql -uroot -p
Enter password: [输入原来的密码]
mysql>use mysql;
mysql> update user set password=password("123456") where user='root';
mysql> flush privileges;
mysql> exit

登录
# mysql -uroot -p123456
```

6. 默认安装位置说明
```
#数据库目录      /var/lib/mysql/ 
#配置文件        /usr/share/mysql    --mysql.server命令及配置文件
#相关命令        /usr/bin            --mysqladmin mysqldump等命令
#启动脚本   　　 /etc/init.d/ 
```
7. 安装完成后，你可以在目录/usr下找到my.cnf，不过这个my.cnf没什么内容，下面配置一下my.cnf，该配置仅作参考使用[可以不配置]
```
[plain] 
# The following options will be passed to all MySQL clients  
[client]  
#password   = your_password  
port        = 3306  
socket      = /opt/mysql/mysql.sock  
 
#Here follows entries for some specific programs  
#The MySQL server  
[mysqld]  
port        = 3306  
socket      = /opt/mysql/mysql.sock  
#Change following line if you want to store your database elsewhere  
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

#由于datadir路径配置为“/opt/mysql”，则需要把默认路径"/var/lib"下整个"mysql"文件夹复制到"/opt"下。以下是需要特别注意的地方：先查看一下"/var/lib"下"mysql"的权限和所有者；发现该文件夹的所有者为mysql。再查看"/opt"下刚才复制的"mysql"文件夹所有者，发现该所有者是root，此时如果启动mysql服务，会报错误：StartingMySQL.The server quit without updating PID file。修改"/opt"目录下的"mysql"文件夹所有者：
/opt>chown -R mysql:mysql mysql

注意：[client] socket路径的配置、[mysqld] socket路径的配置、[safe_mysqld] socket路径的配置、[mysqldump] socket路径的配置，都是配置在"/opt/mysql"下。否则会报没有找到mysql.sock错误。
```

8. 可能遇到的问题
```
Q1 mysql:mysql is neither service nor target!?   --启动mysql服务报错
	A 显示此服务，启动mysql服务
		# systemctl unmask mysql.service
		# service mysql start
		
Q2 Starting MySQL...The server quit without updating PID file (/var/lib/mysql/linux-bfa4.pid)...failed   --启动mysql服务报错
	A 导致此问题的原因较多，查看日志,进一步定位具体原因
		# vi /var/lib/mysql/linux-bfa4.err
  
```
9. 卸载
```
###排除安装包
#rpm -qa | grep -i mysql
#rpm -e 
	rpm -e MySQL-test-5.6.32-1.sles11.x86_64
	rpm -e MySQL-embedded-5.6.32-1.sles11.x86_64
	rpm -e MySQL-devel-5.6.32-1.sles11.x86_64   
	rpm -e MySQL-shared-5.6.32-1.sles11.x86_64
	rpm -e MySQL-shared-compat-5.6.32-1.sles11.x86_64
	rpm -e MySQL-client-5.6.32-1.sles11.x86_64
	rpm -e MySQL-server-5.6.32-1.sles11.x86_64

###查看服务是否存在，端口是否被占用

#chkconfig -list | grep –i mysql
#ps -ef|grep mysql |grep -v grep
#netstat -nat | grep 3306
#netstat -lp | grep 3306

#chkconfig -del mysql 
#kill [process/pid]

###删除相关文件夹
#whereis mysql
#rm -rf /usr/share/mysql
#rm -rf /var/lib/mysql
#find / -name mysql
```
