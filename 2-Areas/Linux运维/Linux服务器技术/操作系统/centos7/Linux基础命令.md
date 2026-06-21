# Linux基础命令

Linux 系统基础命令速查表。



## 基础命令
 tar -zcvf tmp.tar.gz /tmp/   #把/temp/目录直接打包压缩为".tar.gz"格式，通过"-z"来识别格式，"-cvf"和打包选项一致

zip -r mydata.zip mydata				压缩文件夹为zip文件

zip mydata.zip mydata01 mydata02.txt 		mydata01文件夹和mydata02.txt压缩成为mydata.zip

unzip mydata.zip -d mydatabak  	 			把mydata.zip解压到mydatabak目录里面

unzip mydata.zip				直接解压mydata.zip文件

unzip -v mydata.zip             		查看mydata.zip文件里面的内容

clear 						清屏

poweroff 					硬关机

reboot 						重启

date 						查看主机时间

hostnamectl set-hostname        	自定义名称  （修改主机名）

history 						查看历史命令

 watch -n 1 （命令）   			每隔1秒执行一次括号里的命令

ss -tnl								  查看服务开放的端口

netstat -tlunp                                         		  查看开放的端口    #需要安装net-tools

source  路径  							  重新加载配置文件

timedatectl set-timezone "Asia/shanghai"	  设置时间为亚洲上海

mv   aa aaa    				移动  (把aa移动到aaa里面)aaa是存放aa的目录也可以使用绝对路径

cp 123.txt aa   			复制 跟移动相似

ls						显示当前目录里面的文件或子目录 -l -a(显示隐藏文件)

touch					新建普通文件

rm						删除文件或目录

rmdir					删除空目录

mkdir					新建目录 -p(一次性创建目录和其子目录)

man ＋命令 				查看命令的用法和说明

cat ＋文件				查看文件或编辑文件内容

tail -10 -f(实时更新数据)+文件 		默认查看后10行数据

head  -n 10  			用于查看文件的开头部分的内容 默认查看前10行数据

pwd					显示当前所在目录

cd 					进入目录

tab					 补齐命令，可以快速补齐想要输入的内容

su -  				Ubuntu进入root模式

su [username]      		登录某个用户

systemctl  stop  network 		关闭网卡

    status  			 查看服务状态

                start 				打开

                restart  				重启

systemctl  disable 服务  			开机自动关闭服务

systemctl  enable 服务   			开机自启

 systemctl list-unit-files |grep httpd  查看服务是否开机自启

rpm -qa | grep vsftpd  		查看是否已经安装vsftpd

yum clean all 				清理软件源缓存

yum repolist 			        列出当前yum源

yum list 					列出所有软件包

yum list |grep httpd*     		查找带有httpd字段的软件包

chmod 修改文件权限 例如 chmod a+rwx abc.txt    chmod ugo+rwx abc.txt

a 表示所有用户  u表示拥有者  g 表示组内用户 o表示其他用户

chmod  777 abc.txt   7=4+2+1  4=r=可读  2=w=可写 1=x=可执行

newusers +(newusers后面直接跟一个文件，文件格式和/etc/passwd的格式相同【用户名1:x:UID:GID:用户说明:用户的家目录:所用SHELL】。) 批量创建用户

fdisk 分盘命令

mkfs.ext4 初始化硬盘格式

mount     -a 重新加载fstab下的挂载点  可以取消挂载也可以重新挂载

mount -o loop /mnt    /root/centos7.iso   挂载一个回环设备列

umount

lsblk						查看硬盘挂载情况

df 						查看挂载点 -T（查看硬盘格式）-h(人性化显示存储大小）

top 						(查看当前内存使用量)

 cat /etc/redhat-release        （查看centos7的内核版本）

### 查找文件
find /etc -name  fstab

作用：从当前目录递归的搜索文件

原理：遍历当前工作目录及其子目录，find命令是在硬盘上遍历查找，非常耗费硬盘资源，查找效率较低

适用场合：能用which、whereis和locate的时候尽量不要用find

which

作用：从环境变量PATH中，定位、返回与指定名字相匹配的可执行文件所在的路径

原理：执行which命令时，which会在当前环境变量PATH中依次寻找能够匹配所找命令名字的可执行文件

适用场合：一般用于查找命令、可执行文件所在的路径

whereis

作用：定位、返回与指定名字匹配的二进制文件、源文件和帮助手册文件

原理：whereis命令首先会去掉filename中的前缀空格和以.开头的任何字符，然后再在数据库（var/lib/slocate/slocate.db）中查找与上述处理后的filename相匹配的二进 制文件、源文件和帮助手册文件，使用之前可以使用updatedb命令手动更新数据库

适用场合：二进制文件、源文件和帮助手册文件路径的查找。

locate

作用：从数据库建立的索引中找，不同的是该命令查找所有部分匹配的文件，使用之前可以使用updatedb命令手动更新数据库

原理：默认情况下(当filename中不包含通配符*)，locate会给出所有与*filename*相匹配的文件的路径。

适用场合：没有文件类型性质的模糊查找（你只记得某个文件的部分名称）

![[1679568310222-4601ddd4-db03-4f3b-bf7b-9bf882cd0f51.png]]
