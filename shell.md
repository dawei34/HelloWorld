```
1.用户
 创建用户：useradd  该命令需要用root用户执行
           useradd -m pxl  创建一个pxl用户并创建默认户家目录/home/pxl。-m：Create home directory for the new user -d指定用户家目录。useradd -d /user/pxl -m pxl
		   useradd -s /usr/bin/csh -g group -m pxl    创建一个pxl用户并指定登录的shell为csh 如果不指定shell默认为bash.指定用户组 为group 当然这个用户组需要存在，指定默认为users
 修改用户密码：passwd pxl  修改pxl 用户的密码
 删除用户： userdel -fr pxl 删除用户pxl 并删除用户家目录
 添加用户组：groupadd group 创建用户组group 
             groupadd -g 101 group 创建用户组group 并指定用户组ID
 删除用户组：groupdel group 删除用户组group
 修改用户组ID： groupmod -g 102 group2
 /etc/passwd文件中记录了所有用户的一些信息，下面是其中任意的两条  
 root:x:0:0:root:/root:/bin/bash
 es:x:3205:100::/home/es:/usr/bin/csh
 这个每一条就是一个用户信息。以：分割可以分为7个域
 第1域是登录名，第2域是加密的密码，第5域是用户全名。第6域是用户根目录，第7域是用户使用的shell。能力中心创建的用户都是csh。

2.文件安全和权限

  一个文件一经创建，就具有三种访问方式：
    1) 读，可以显示该文件的内容。
    2) 写，可以编辑或删除它。
    3) 执行，如果该文件是一个shell脚本或程序。
 示例：drwxrwxrwx 8 es   users      4096 Mar 24 16:17 j2se
  drwxrwxrw- 可以分为四个段来看第一个d表示文件类型后面的六位是文件的权限前三位是本有用户的权限，中间的三位是同用户组的权限最后三位是其他用户的权限。具体含义后面介绍。
 2.1 文件权限
     r 读权限
     w 写/更改权限
     x 执行该脚本或程序的权限
 2.2文件类型
    d 目录。
    l 符号链接(指向另一个文件)。
    s 套接字文件。
    b 块设备文件。 
    c 字符设备文件。
    p 命名管道文件。
    - 普通文件，或者更准确地说，不属于以上几种类型的文件
 2.3 chmod 修改文件权限
		chmod有两种方式修改文件的权限。
		第一种是通过+ -权限 例：chmod +x myfile  给所有用户加可执行权限 chmod u+x myfile 赋予文件属主执行权限 chmod g+x myfile 赋予同组用户执行权限 这种方式还是比较复杂的，我是不喜欢用这种权限
		第二种是通过计算二进制的方式 例：
		chmod 666 rw- rw- rw- 赋予所有用户读和写的权限
        chmod 644 rw- r-- r- - 赋予所有文件属主读和写的权限，所有其他用户读权限
        chmod 744 rwx r-- r- - 赋予文件属主读、写和执行的权限，所有其他用户读的权限
        chmod 664 rw- rw- r- - 赋予文件属主和同组用户读和写的权限，其他用户读权限
        chmod 700 rwx --- --- 赋予文件属主读、写和执行的权限
		chmod 444 r-- r-- r- - 赋予所有用户读权限
		希望使自己对该文件可读、写和执行， 同组用户对该文件只读，可以键入：chmod 740 myfile
		常用的还有一个参数 -R 例：chmod -R 740 myfile   将这myfile目录下面所有的文件权限全部修改成740权限
  2.4 chown
		当你创建一个文件时，你就是该文件的属主。一旦你拥有某个文件，就可以改变它的所有权，把它的所有权交给另外一个/ e t c / p a s s w d文件中存在的合法用户。可以使用用户名或用
	户I D号来完成这一操作。在改变一个文件的所有权时，相应的s u i d也将被清除，这是出于安全性的考虑。只有文件的属主和系统管理员可以改变文件的所有权。一旦将文件的所有权交
	给另外一个用户，就无法再重新收回它的所有权。如果真的需要这样做，那么就只有求助于系统管理员了。
	常用的方式有chown pxl:users myfile 将文件or目录myfile所属修改为用户为pxl 用户组为users
	chown -R pxl:users myfile 修改myfile目录下所有文件和目录的所属。
  //2.5 umask 设置用户创建文件和目录的权限，可以执行umask查看值。
		当最初登录到系统中时， u m a s k命令确定了你创建文件的缺省模式。这一命令实际上和chmod命令正好相反。你的系统管理员必须要为你设置一个合理的umask值，以确保你创建的
	文件具有所希望的缺省权限，防止其他非同组用户对你的文件具有写权限。
		一般来说，umask命令是在/etc/profile文件中设置的，每个用户在登录时都会引用这个文件，所以如果希望改变所有用户的umask，可以在该文件中加入相应的条目。如果希望永久性
	地设置自己的umask值，那么就把它放在自己$HOME目录下的.profile或.bashprofile文件中。
  // 2.5.1 如何计算umask值
    例如，对于umask值002，相应的文件和目录缺省创建权限是什么呢？
	第一步，我们首先写下具有全部权限的模式，即777(所有用户都具有读、写和执行权限)。
	第二步，在下面一行按照umask值写下相应的位，在本例中是002。
	第三步，在接下来的一行中记下上面两行中没有匹配的位。这就是目录的缺省创建权限。
	第四步，对于文件来说，在创建时不能具有文件权限，只要拿掉相应的执行权限比特即可。
	这里例举两个例子：
		umask值为002：
			1) 文件的最大权限rwx rwx rwx (777)
			2) umask值为0 0 2 - - - - - - -w-
			3) 目录权限rwx rwx r-x (775) 这就是目录创建缺省权限
			4) 文件权限rw- rw- r-- (664) 这就是文件创建缺省权限
        umask值为022：
			1) 文件的最大权限rwx rwx rwx (777)
			2) umask值为022 - - - -w- -w-
			3) 目录权限rwx r-x r-x (755) 这就是目录创建缺省权限
			4) 文件权限rw- r-- r-- (644) 这就是文件创建缺省权限
	2.6 符号链接
			存在两种不同类型的链接，软链接和硬链接，这里我们只讨论软链接。软链接实际上就是一个指向文件的指针。你将会发现这种软链接使用起来非常方便。
		该命令的一般形式为：ln [-s] source_path target_path 这个不常用，有兴趣的同学可以自己百度了解一下。
3 find 命令
	一般常用的方式是 find /home -name myfile 在/home目录下面(所有子目录)查找myfile
	find /home -type d -name myfile  指定搜索类型。 假定myfile存在。如果myfile是文件则搜索不到。如果是目录则能搜索到。-f 搜索文件，-l 搜索软连接
	find . -name a.th -exec rm {} \; 当前目录下查找并删除a.th文件 
	find 还能根据修改时间搜索等功能。功能比较强大，因为不常用，这里就不介绍了。
4 正则表达式

	^ 只只匹配行首
	$ 只只匹配行尾
	* 只一个单字符后紧跟*，匹配0个或多个此单字符
	[ ] 只匹配[ ]内字符。可以是一个单字符，也可以是字符序列。可以使用-表示[ ]内字符序列范围，如用[ 1 - 5 ]代替[ 1 2 3 4 5 ]
	\ 只用来屏蔽一个元字符的特殊含义。因为有时在shell中一些元字符有特殊含义。\可以使其失去应有意义
	. 只匹配任意单字符
	pattern\{ n \ } 只用来匹配前面pattern出现次数。n为次数
	pattern\{ n，\ } m 只含义同上，但次数最少为n
	pattern\{ n，m \ } 只含义同上，但pattern出现次数在n与m之间
5 grep
	相信grep是UNIX和LINUX中使用最广泛的命令之一。grep（全局正则表达式版本）允许对文本文件进行模式查找。如果找到匹配模式， grep打印包含模式的所有行。grep支持基本正则表达式，也支持其扩展集
 这里例举一些grep常用的方法；
   grep "pxl" *.doc  在当前目录下所有.doc文件中查找字符串"pxl"，
   grep -c 6850 6850.conf    6850.conf中有几行含有字符串6850
   grep  6850 6850.conf     6850.conf含有6850的行显示出来
   ls -l |grep '^d'  列出当前目录下所有目录。
   grep "es" /etc/passwd 文件匹配字符串
   ps -ef | grep java    ps -ef 查看系统中运行的进程;

6.sed
    sed是一个非交互性文本流编辑器。它编辑文件或标准输入导出的文本拷贝。标准输入可能是来自键盘、文件重定向、字符串或变量，或者是一个管道的文本。
	# sed编辑器逐行处理文件,读入的当前行存在模式空间中。保留空间（或者称为暂存空间）默认为空行
	# n 输出模式空间行，读取下一行替换当前模式空间的行，执行下一条处理命令而非第一条命令。
	# N 读入下一行，追加到模式空间行后面，此时模式空间有两行。
	# h 把模式空间里的行拷贝到暂存空间。
	# H 把模式空间里的行追加到暂存空间。
	# g 用暂存空间的内容替换模式空间的行。
	# G 把暂存空间的内容追加到模式空间的行后。
	# x 将暂存空间的内容于模式空间里的当前行互换。
	# ！对所选行以外的所有行应用命令。
	# p 打印模式空间中的内容
	# -e 是编辑命令
	# -i 直接修改文件
	# = 显示文件行号
	# [ \t] tab和空格，[\t]为tab

	sed '/^$/d' ${file}         #删除空行
	sed q  ${file}              #输出第一行
	sed 3q  ${file}             # 输出前三行
	sed -n '2p' ${file}         #输出第二行
	sed -n '$p' ${file}         #输出最后一行， $表示最后一行
	sed -n '1,3p' ${file}       #输出第一行到第三行
	sed -n '/abcd/' ${file}     #输出匹配的行
	sed -n '1,$p'  ${file}      #输出第一行到最后一行
	sed -n '/.*B/p' ${file}     #匹配一次或者多次
	sed -n '/AA/=' ${file}      #输出匹配行的行号
	sed -n -e '/AA/p' -e '/AA/=' ${file}      #输出匹配行和行号
	sed '1d' ${file}     #删除第1行
	sed '1,3d' ${file}   #删除1到3行
	sed '$d'             #删除最后一行
	sed -n 's/AA/AAXXXX&/p' ${file}        #在匹配字符串前加AAXXXX。 只保留匹配的行
	sed -n 's/.*/&AAXXXX /p' ${file}       #在所有行后面添加AAXXXX
	sed 's/$/AAXXXX/g' ${file}             #在所有行后面添加AAXXXX
	sed '/AA/ w filename' ${file}          #将所有匹配行都写到文件filename
	sed  '/AA/r c.txt' a.txt  ${file}      #在a.txt文件中匹配AA的行下一行插入c.txt
	sed  '/.F.*/q' a.txt ${file}           #输出第一行到匹配行。 任意字符后跟F，再跟任意字符0次或者任意次
	sed "s/^[0-9]*//g"  ${file}            #去除行首数字；
	sed 's/^[ \t]*//;s/[ \t]*$//' ${file}  #组合命令，删除开头和结尾的tab和空格
	sed '1,3a bbbb' ${file}                #第一行到第三行每行下面添加bbbb
	sed '1,2c Hi'   ${file}                #第一到第二行填换成 Hi 

	sed G ${file}      #使一个文件中每一行都占用两行空间(就是在每一行后边插一行空行)

	sed  '/^$/d' ${file}  #查找并删除空行

	sed '/^$/d;G' ${file}  #查找并删除空行；然后用 sed G 插入空行

	sed '/regex/{x;p;x;} ${file}   #含有regex的行上面添加一行空行。 


	sed '/regex/G' ${file}      #在每个含有字符串regex的行下插入一行空白行
	sed '{n;d}'  ${file}        #删除偶数行 
	sed '{n;G}'  ${file}        #偶数行添加空行
	sed '/regex/{x;p;x;G;}'     #在每个含有字符串regex的行上，下各插入一行空白行

	sed = ${file} | sed 'N;s/\n/\t/'  每行加上序号，并用tab分开
	# sed = filename的功能是 Print the current line number. （这个功能是在每一行前面另加一行，并且显示行号）
	# sed 'N;s/\n/\t/'  这个时候模式空间为两行，然后将\n(换行)替换成\t（tab制表符）
	# 直接修改源文件 sed -i = ${file} | sed -i 'N;s/\n/\t/' ${file}

	sed '/./=' ${file} | sed '/./N; s/\n/ /'  给文件每一行加上数字序号，但是仅当行非空时打印数字。空行也站一行。只是空行前的序号不输出、
	#sed '/./=' ${file}  非空时加上序号
	#sed '/./N; s/\n/ /'查找非空行并把后一行附加到当前行,然后用空格替换换行符\n

	sed -n '$='  统计行等同于cat a.txt |wc -l
	#参数n限制自动打印模式空间中的类容 = 打印行号   '$='中$的含义是Match the last line
	
7.awk
	如果要格式化报文或从一个大的文本文件中抽取数据包，那么awk可以完成这些任务。它在文本浏览和数据的熟练使用上性能优异。
	awk 默认以 空格分割：
	ll |awk '{print $1}' 列出第一列
	cat /etc/passwd |awk -F: '{print $1}'
	cat /etc/passwd |awk  -F ':'  '{print $1"\t"$7}'显示第一列和第7列。中间tab分割
8.一些常用的命令
cd: 进入目录
cat:  cat myfile 
more：一页一页的显示档案内容
head：head -n 10 /etc/passwd 看文件前10行
tail：tail -n 10 /etc/passwd 看文件的最后10行
tailf：tailf -10 xxx.log  
kill -9 杀进程
cp：  cp /home/pxl /home/user/   将文件pxl cp到/home/user/目录下
mkdir: mkdir /home/pxl/1/2/3   创建目录3，前面的目录要都存在； -p 参数目录不存在的时候也会创建 
touch ：touch myfile 创建文件
pwd ：显示当前的路径
who ：显示当前哪些用户登录了
whoami:显示当前的用户
rmdir：删除空目录。
rm: 删除文件：rm myfile、删除目录 rm -fr mydir
ls 和ll :例出目录下所有文件。-a 参数可以显示隐藏文件
wc 统计字符和行数；
	who |wc  who命令的输出通过管道传递给wc命令，该命令显示出如下的几列：行数、单词数、字符数  
	ll |wc -l 显示目录下面有多少行
date:显示时间
mv：移动文件
top  够实时显示系统中各个进程的资源占用状况
解压打包命令：
.tar.gz
解包：tar xvf FileName.tar
打包：tar cvf FileName.tar DirName
.zip
解压：unzip FileName.zip
压缩：zip FileName.zip DirName
lsof -i:port  查看端口监听情况
vi： 命令
	vi filename :打开或新建文件，并将光标置于第一行首 
	屏幕翻滚类命令 
		Ctrl+u：向文件首翻半屏 
		Ctrl+d：向文件尾翻半屏 
		Ctrl+f：向文件尾翻一屏 
		Ctrl＋b；向文件首翻一屏 
	插入文本类命令 
		i ：在光标前 
		I ：在当前行首 
		a：光标后 
		A：在当前行尾 
   搜索及替换命令 
	/pattern：从光标开始处向文件尾搜索pattern 
	?pattern：从光标开始处向文件首搜索pattern 	
	n：在同一方向重复上一次搜索命令 
	N：在反方向上重复上一次搜索命令 
	:10 光标移动到第10行
	:$ 光标移动到最后一行
	：s/p1/p2/g：将当前行中所有p1均用p2替代 
	：n1,n2 s/p1/p2/g：将第n1至n2行中所有p1均用p2替代 
	：g/p1/s//p2/g：将文件中所有p1均用p2替换 
	删除命令： 
		dd: 删除当前行
		ndd：删除当前行及其后n-1行 
		x或X：删除一个字符，x删除光标后的，而X删除光标前的 
	最后行命令
	:w 保存当前文档
	:x 保存退出
	:q 退出编辑（没有修改文档）
	:q! 不保存退出（已经修改了文档）
	移动光标：
	┌─────┬───────────┐ 
	│命令　　　│描述　　　　　　　　　│ 
	├─────┼───────────┤ 
	│j或下箭头 │向下移动一行　　　　　│ 
	├─────┼───────────┤ 
	│k或上箭头 │向上移动一行　　　　　│ 
	├─────┼───────────┤ 
	│h或左箭头 │左移一个字符　　　　　│ 
	├─────┼───────────┤ 
	│l或右箭头 │右移一个字符　　　　　│ 
	├─────┼───────────┤ 
	│w 　　　　│右移一个词　　　　　　│ 
	├─────┼───────────┤ 
	│W 　　　　│右移一个以空格分隔的词│ 
	├─────┼───────────┤ 
	│b 　　　　│左移一个词　　　　　　│ 
	├─────┼───────────┤ 
	│B 　　　　│左移一个以空格分隔的词│ 
	├─────┼───────────┤ 
	│0 　　　　│移到行首　　　　　　　│ 
	│Ctrl-F　　│向前翻页　　　　　　　│ 
	├─────┼───────────┤ 
	│Ctrl-B　　│向后翻页　　　　　　　│ 
	├─────┼───────────┤ 
	│nG　　　　│到第n行 　　　　　　　│ 
	├─────┼───────────┤ 
	│G 　　　　│到最后一行　　　　　　│ 	
	└─────┴───────────┘ 
	

9 shell 脚本
		shell脚本在处理自动循环或大的任务方面可节省大量的时间，且功能强大。如果你有处理一个任务的命令清单，不得不一个一个敲进去，然后观察输出结果，再决定它是否正确，
	如果正确，再继续下一个任务，否则再回到清单一步步观察。一个任务可能是将文件分类、向文件插入文本、迁移文件、从文件中删除行、清除系统过期文件、以及系统一般的管理维
	护工作等等。创建一个脚本，在使用一系列系统命令的同时，可以使用变量、条件、算术和循环快速创建脚本以完成相应工作。这比在命令行下一个个敲入要节省大量的工作时间。
	shell脚本可以在行命令中接收信息，并使用它作为另一个命令的输入
	简单的举一个例子：
	#!/bin/sh
	ehco "starting cleanup ...wait"
	rm /usr/lcoal/apps/log/*.log
	tail -40 /var/adm/mesages > /tmp/messages
	rm /var/adm/messages
	mv /tmp/messages /var/adm/messages #将/tmp/messages移动到/var/adm/messages
	if [ "$?" = 0 ];then
		ecoh "mv successfully"
	else
		ecoh "mv failed"
	fi
	
	
	脚本需要有可执行权限才能被执行。

```
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
