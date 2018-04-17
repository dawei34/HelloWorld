1. 添加用户及用户组
```
#groupadd mysql
#useradd -g mysql mysql -d /home/mysql -m
```
2. 添加压缩包
```
#tar -zxvf mysql-5.6.28.tar.gz
#mv mysql-5.6.28/* mysql
```
3. 修改属主
```
#chown -R mysql:mysql /home/mysql
```
4. 安装MySql数据库
```
/home/mysql/scripts/mysql_install_db --user=mysql --basedir=/home/mysql --datadir=/home/mysql/data
```
5. 修改mysql的配置文件，my.cnf
```
查看/etc文件夹下是否有my.cnf文件，没有copy或者新建my.cnf文件
	copy文件
	#cp my-default.cnf /etc/my.cnf
	新建文件
	#vi my.cnf
	编辑文件
	#vi /etc/my.cnf
		[mysqld]
		basedir=/home/mysql
		datadir=/home/mysql/data
		socket=/home/mysql/data/mysql.sock
		[client]
		default-character-set=utf8
		socket=/home/mysql/data/mysql.sock
		[mysql]
		default-character-set=utf8
		socket=/home/mysql/data/mysql.sock
```		
6. 启动mysql服务和添加开机启动mysql服务
```
添加开机启动,把启动脚本
	#cp /home/mysql/support-files/mysql.server /etc/init.d/mysql
	修改启动文件
	#vi /etc/init.d/mysql
		#i
		basedir=/home/mysql
		datadir=/home/mysql/data
```		
7. 配置mysql开机启动
```
# chkconfig --add mysql
# chkconfig mysql on
```	
8. 配置环境变量
```
# vi ~/.bashrc
	export MYSQL_HOME=/usr/local/mysql
	export PATH=$PATH:$MYSQL_HOME/bin
# source ~/.bashrc
```
9. 启动mysql服务并赋权限修改密码
```
# /etc/init.d/mysql start
# /etc/init.d/mysql status
	启动mysql服务
	#service mysql start
	执行命令，查看mysql服务是否启动成功
```
10. 修改mysql的root密码
```
#./bin/mysqladmin -u root password 'root'
```
