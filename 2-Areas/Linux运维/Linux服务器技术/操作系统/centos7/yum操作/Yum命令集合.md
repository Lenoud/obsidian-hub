# Yum命令集合

yum针对软件包操作常用命令：
1.使用YUM查找软件包
命令：yum search php

![[1684981722067-a58dff50-956a-42da-8207-a83e8de2ef71.png]]

2.列出所有可安装的软件包
命令：yum list php

![[1684981722203-88766046-8190-4e85-b923-1ad9f4c60d17.png]]

3.列出所有可更新的软件包
命令：yum list updates

4.列出所有已安装的软件包
命令：yum list installed

5.列出所有已安装但不在 Yum Repository 内的软件包
命令：yum list extras

6.列出所指定的软件包
命令：yum list +包名

7.使用YUM获取软件包信息 、**显示yum包的信息：**
命令：yum info PACKAGE_NAME

8.**搜索yum包：**
命令：yum search PACKAGE_NAME

9.列出所有可更新的软件包信息
命令：yum info updates

10.列出所有已安装的软件包信息
命令：yum info installed

11.列出所有已安装但不在 Yum Repository 内的软件包信息
命令：yum info extras

12.列出软件包提供哪些文件
命令：yum provides

13、**更新具体的yum包：**

```bash
$ yum update PACKAGE_NAME
```

**14.显示已启用的yum存储库的列表：**

```bash
$ yum repolist
```

15.清除yum缓存：

$ yum clean all

```bash
$ yum clean all
```

**16.找出哪个yum包提供了一个特定的文件（例如：/usr/bin/nc)）：**

```ruby
$ yum whatprovides "*bin/nc"
```

**17.卸载yum包装：**

```csharp
$ yum remove PACKAGE_NAME
```

**18.取出yum包装：**

```bash
$ yum downloader PACKAGE_NAME
```

**20.重新安装一个yum包：**

```bash
$ yum reinstall PACKAGE_NAME
```

**查到某些软件是否安装了。总结起来就是这样几类：**

1、rpm包安装的，可以用rpm -qa看到，如果要查找某软件包是否安装，用 rpm -qa | grep “软件或者包的名字”。

[root[@localhost](https://my.oschina.net/u/570656) ~] rpm -qa | grep ruby

2、**以deb包安装的，可以用dpkg -l能看到**。如果是查找指定软件包，用dpkg -l | grep “软件或者包的名字”；

[root[@localhost](https://my.oschina.net/u/570656)  ~] dpkg -l | grep ruby

3、yum方法安装的，可以用yum list installed查找，如果是查找指定包，命令后加 | grep “软件名或者包名”；

[root[@localhost](https://my.oschina.net/u/570656) ~] yum list installed | grep ruby

4、如果是以源码包自己编译安装的，例如.tar.gz或者tar.bz2形式的，这个只能看可执行文件是否存在了，

上面两种方法都看不到这种源码形式安装的包。如果是以root用户安装的，可执行程序通常都在/sbin:/usr/bin目录下。

说明：

其中rpm yum Redhat系linux的软件包管理命令，dpkg debian系列的软件包管理命令

## 5、安装一个软件所有依赖的包
yum localinstall -y java.1.1.0.rpm

## **软件的配置管理**
1）Linux平台下软件分类，按照软件的内容分为**二进制软件、源码包软件；**

2）二进制包特点：软件的内容直接可以使用的，系统能够直接识别，直接运行，后缀以rpm、.zip结尾，或者基于rpm、yum工具去安装；

**3）源代码包特点：**软件的内容不能直接使用的，内容包括.c .h .cpp等，**后缀以tar、zip、tar.gz、tar.bz2，**需要通过GCC编译器编译，生成二进制文件，方可使用；安装的方式：./configure;make;make install；

4）RPM软件、YUM软件区别是什么？没有大的区别，都是用于管理以.rpm结尾的二进制包，RPM、YUM可以实现软件的安装、卸载、更新等管理；

5）RPM软件管理不能自己解决依赖软件包，而YUM可以自行解决各种依赖包，企业生产环境推荐使用YUM工具的，RPM安装的软件包，必须在本地存在（也可以使用http下载），YUM安装的软件包可以在线自动下载；

6）为嘛YUM可以自行下载软件，因为服务器可以上网，YUM内部工作机制问题，**YUM是C/S模式**，客户端、服务端，**客户端基于repo文件找到服务端镜像地址**，根据地址镜像rpm软件安装、配置，如果镜像地址是外网，需要服务器能够上外网；

7）YUM服务器端负责发布工作.rpm结尾软件包+依赖关系+基础数据库信息，服务器端一般通过HTTP、FTP协议进行发布；

8）YUM客户端，基于YUM命令，自动去查找YUM服务器端相关的软件+依赖关系，**客户端使用YUM命令，首先会检查/etc/yum.repos.d是否有.repo结尾的文件，如果没有repo结尾的文件，则无法使用yum安装软件；**

9）BAT企业，都是内部构建本地YUM源，YUM在内部节约外部带宽，节省成本，同时加快运行效率；

10）服务器内部传输带宽至少1000Mb，

## **YUM源端软件包扩展**
## YUM源端软件包扩展
默认使用ISO镜像文件中的软件包构建的HTTP YUM源，会发现缺少很多软件包，如果服务器需要挂载移动硬盘，Mount挂载移动硬盘需要ntfs-3g软件包支持，而本地光盘镜像中没有该软件包，此时需要往YUM源端添加ntfs-3g软件包，添加方法如下：

+ 切换至/var/www/html/centos目录，官网下载NTFS-3G软件包。

| cd /var/www/html/centos/

wget http://dl.fedoraproject.org/pub/epel/7/x86_64/n/ntfs-3g-2016.2.22-3.el7.x86_64.rpm
http://dl.fedoraproject.org/pub/epel/7/x86_64/n/ntfs-3g-devel-2016.2.22-3.el7.x86_64.rpm |
| :--- |

+ Createrepo命令更新软件包，同理，如需新增其他软件包，同样把软件下载至本地，然后通过createrepo更新即可，如图6-18所示：

| createrepo –update centos/ |
| :--- |

![[1684981722249-ac3b20a9-c04a-41e9-980a-e2be9084de58.png]]

图6-18 CreateRepo update更新软件包

+ 客户端YUM验证，安装NTFS-3G软件包，如图6-19所示：

![[1684981722292-e4d8b00d-855a-481b-8f4b-73365b25dc0e.png]]

常见问题：

1、yum install ntpdate，报错如下：

Loaded plugins: fastestmirror, priorities

http://mirror.centos.org/centos/7/os/x86_64/repodata/repomd.xml: [Errno 14] curl#6 - "Could not resolve host: mirror.centos.org; Name or service not known"

Trying other mirror.

Could not resolve host不能解析地址

解决方法两种：

1. Ping mirror.centos.org是否能够返回IP地址，检测服务器DNS配置和网关配置，是否正确，问题可以被解决；

修改配置文件DNS：vim /etc/resolv.conf

2、执行rpm -e vsftpd指令，报错信息如下：

error: Failed dependencies:

vsftpd = 3.0.2-22.el7 is needed by (installed) vsftpd-sysvinit-3.0.2-22.el7.x86_64

解决方法两种：

1. rpm -e vsftpd-sysvinit vsftpd 卸载依赖的包；
2. rpm -e vsftpd --nodeps 不依赖其他的包，可能会不完整；

![[1684981722396-b0dba805-6d6b-447b-8b65-5f9fe6b8113f.png]]

error: open of vsftpd-3.0.2-22.el7.x86_64.rpm failed: No such file or directory

解决方法两种：

1. 找不到该文件，从光盘镜像ISO找到该文件，然后上传至当前目录；
2. 可以使用rpm -ivh在线安装，在百度上面查找，然后复制地址，例如： rpm -ivh [http://rpmfind.net/linux/centos/7.4.1708/os/x86_64/Packages/vsftpd-3.0.2-22.el7.x86_64.rpm](http://rpmfind.net/linux/centos/7.4.1708/os/x86_64/Packages/vsftpd-3.0.2-22.el7.x86_64.rpm)

###
### **3、CentOS7yum安装出现/var/run/yum.pid 已被锁定，解决办法 ： **
root[@bogon](https://my.oschina.net/u/3914826) ~]# yum install -y gcc gcc+
已加载插件：fastestmirror, langpacks
/var/run/yum.pid 已被锁定，PID 为 11154 的另一个程序正在运行。
Another app is currently holding the yum lock; waiting for it to exit...
另一个应用程序是：PackageKit
内存：130 M RSS （1.4 GB VSZ）
已启动： Mon May  1 16:17:14 2017 - 04:06之前
状态  ：睡眠中，进程ID：11154
Another app is currently holding the yum lock; waiting for it to exit...
另一个应用程序是：PackageKit
内存：130 M RSS （1.4 GB VSZ）
已启动： Mon May  1 16:17:14 2017 - 04:08之前
状态  ：睡眠中，进程ID：11154

经过百度发现只要删除/var/run/yum.pid就可以正常使用了，即

rm -rf /var/run/yum.pid.
/sbin/service yum-updatesd restart

4、yum 安装软件时，报错：No package XXX available.

[root[@localhost](https://my.oschina.net/u/570656) ~]# yum -y install redis
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
* addons: mirrors.163.com
* base: mirrors.163.com
* extras: mirrors.163.com
* updates: mirrors.163.com
Setting up Install Process
No package redis available.
Nothing to do
解决方法：

1）.先去更新一下yum仓库：
#yum -y update

5、yum安装软件报错：curl#6 - "Could not resolve host: mirrorlist.centos.org; Temporary failure in name resolut

# yum install -y epel-release
Loaded plugins: fastestmirror
Repository base is listed more than once in the configuration
Repository updates is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=stock error was
14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Temporary failure in name resolution"

1. Contact the upstream for the repository and get them to fix the problem.

2. Reconfigure the baseurl/etc. for the repository, to point to a working
upstream. This is most often useful if you are using a newer
distribution release than is supported by the repository (and the
packages for the previous distribution release still work).

dns解析的问题，处理办法：
vim /etc/resolv.conf  加入：

nameserver 8.8.8.8
nameserver 8.8.4.4
search localdomain

yum针对软件包操作常用命令：
1.使用YUM查找软件包
命令：yum search php

![[1684981722504-8fb52307-6410-4578-af4d-8e4d98114704.png]]

2.列出所有可安装的软件包
命令：yum list php

![[1684981722976-e3970d5a-f2ff-4292-9361-3fe8f5ca18ab.png]]

3.列出所有可更新的软件包
命令：yum list updates

4.列出所有已安装的软件包
命令：yum list installed

5.列出所有已安装但不在 Yum Repository 内的软件包
命令：yum list extras

6.列出所指定的软件包
命令：yum list +包名

7.使用YUM获取软件包信息 、**显示yum包的信息：**
命令：yum info PACKAGE_NAME

8.**搜索yum包：**
命令：yum search PACKAGE_NAME

9.列出所有可更新的软件包信息
命令：yum info updates

10.列出所有已安装的软件包信息
命令：yum info installed

11.列出所有已安装但不在 Yum Repository 内的软件包信息
命令：yum info extras

12.列出软件包提供哪些文件
命令：yum provides

13、**更新具体的yum包：**

```bash
$ yum update PACKAGE_NAME
```

**14.显示已启用的yum存储库的列表：**

```bash
$ yum repolist
```

15.清除yum缓存：

$ yum clean all

```bash
$ yum clean all
```

**16.找出哪个yum包提供了一个特定的文件（例如：/usr/bin/nc)）：**

```ruby
$ yum whatprovides "*bin/nc"
```

**17.卸载yum包装：**

```csharp
$ yum remove PACKAGE_NAME
```

**18.取出yum包装：**

```bash
$ yum downloader PACKAGE_NAME
```

**20.重新安装一个yum包：**

```bash
$ yum reinstall PACKAGE_NAME
```

**查到某些软件是否安装了。总结起来就是这样几类：**

1、rpm包安装的，可以用rpm -qa看到，如果要查找某软件包是否安装，用 rpm -qa | grep “软件或者包的名字”。

[root[@localhost](https://my.oschina.net/u/570656) ~] rpm -qa | grep ruby

2、**以deb包安装的，可以用dpkg -l能看到**。如果是查找指定软件包，用dpkg -l | grep “软件或者包的名字”；

[root[@localhost](https://my.oschina.net/u/570656)  ~] dpkg -l | grep ruby

3、yum方法安装的，可以用yum list installed查找，如果是查找指定包，命令后加 | grep “软件名或者包名”；

[root[@localhost](https://my.oschina.net/u/570656) ~] yum list installed | grep ruby

4、如果是以源码包自己编译安装的，例如.tar.gz或者tar.bz2形式的，这个只能看可执行文件是否存在了，

上面两种方法都看不到这种源码形式安装的包。如果是以root用户安装的，可执行程序通常都在/sbin:/usr/bin目录下。

说明：

其中rpm yum Redhat系linux的软件包管理命令，dpkg debian系列的软件包管理命令

## 5、安装一个软件所有依赖的包
yum localinstall -y java.1.1.0.rpm

## **软件的配置管理**
1）Linux平台下软件分类，按照软件的内容分为**二进制软件、源码包软件；**

2）二进制包特点：软件的内容直接可以使用的，系统能够直接识别，直接运行，后缀以rpm、.zip结尾，或者基于rpm、yum工具去安装；

**3）源代码包特点：**软件的内容不能直接使用的，内容包括.c .h .cpp等，**后缀以tar、zip、tar.gz、tar.bz2，**需要通过GCC编译器编译，生成二进制文件，方可使用；安装的方式：./configure;make;make install；

4）RPM软件、YUM软件区别是什么？没有大的区别，都是用于管理以.rpm结尾的二进制包，RPM、YUM可以实现软件的安装、卸载、更新等管理；

5）RPM软件管理不能自己解决依赖软件包，而YUM可以自行解决各种依赖包，企业生产环境推荐使用YUM工具的，RPM安装的软件包，必须在本地存在（也可以使用http下载），YUM安装的软件包可以在线自动下载；

6）为嘛YUM可以自行下载软件，因为服务器可以上网，YUM内部工作机制问题，**YUM是C/S模式**，客户端、服务端，**客户端基于repo文件找到服务端镜像地址**，根据地址镜像rpm软件安装、配置，如果镜像地址是外网，需要服务器能够上外网；

7）YUM服务器端负责发布工作.rpm结尾软件包+依赖关系+基础数据库信息，服务器端一般通过HTTP、FTP协议进行发布；

8）YUM客户端，基于YUM命令，自动去查找YUM服务器端相关的软件+依赖关系，**客户端使用YUM命令，首先会检查/etc/yum.repos.d是否有.repo结尾的文件，如果没有repo结尾的文件，则无法使用yum安装软件；**

9）BAT企业，都是内部构建本地YUM源，YUM在内部节约外部带宽，节省成本，同时加快运行效率；

10）服务器内部传输带宽至少1000Mb，

## **YUM源端软件包扩展**
## YUM源端软件包扩展
默认使用ISO镜像文件中的软件包构建的HTTP YUM源，会发现缺少很多软件包，如果服务器需要挂载移动硬盘，Mount挂载移动硬盘需要ntfs-3g软件包支持，而本地光盘镜像中没有该软件包，此时需要往YUM源端添加ntfs-3g软件包，添加方法如下：

+ 切换至/var/www/html/centos目录，官网下载NTFS-3G软件包。

| cd /var/www/html/centos/

wget http://dl.fedoraproject.org/pub/epel/7/x86_64/n/ntfs-3g-2016.2.22-3.el7.x86_64.rpm
http://dl.fedoraproject.org/pub/epel/7/x86_64/n/ntfs-3g-devel-2016.2.22-3.el7.x86_64.rpm |
| :--- |

+ Createrepo命令更新软件包，同理，如需新增其他软件包，同样把软件下载至本地，然后通过createrepo更新即可，如图6-18所示：

| createrepo –update centos/ |
| :--- |

![[1684981723411-1d9a747a-5158-4e4a-aee9-2998bb32c0ca.png]]

图6-18 CreateRepo update更新软件包

+ 客户端YUM验证，安装NTFS-3G软件包，如图6-19所示：

![[1684981723473-0df27273-3559-4078-a50c-31165c2960fe.png]]

常见问题：

1、yum install ntpdate，报错如下：

Loaded plugins: fastestmirror, priorities

http://mirror.centos.org/centos/7/os/x86_64/repodata/repomd.xml: [Errno 14] curl#6 - "Could not resolve host: mirror.centos.org; Name or service not known"

Trying other mirror.

Could not resolve host不能解析地址

解决方法两种：

1. Ping mirror.centos.org是否能够返回IP地址，检测服务器DNS配置和网关配置，是否正确，问题可以被解决；

修改配置文件DNS：vim /etc/resolv.conf

2、执行rpm -e vsftpd指令，报错信息如下：

error: Failed dependencies:

vsftpd = 3.0.2-22.el7 is needed by (installed) vsftpd-sysvinit-3.0.2-22.el7.x86_64

解决方法两种：

1. rpm -e vsftpd-sysvinit vsftpd 卸载依赖的包；
2. rpm -e vsftpd --nodeps 不依赖其他的包，可能会不完整；

![[1684981723734-4160753f-983a-4e2f-b026-79409a4be11e.png]]

error: open of vsftpd-3.0.2-22.el7.x86_64.rpm failed: No such file or directory

解决方法两种：

1. 找不到该文件，从光盘镜像ISO找到该文件，然后上传至当前目录；
2. 可以使用rpm -ivh在线安装，在百度上面查找，然后复制地址，例如： rpm -ivh [http://rpmfind.net/linux/centos/7.4.1708/os/x86_64/Packages/vsftpd-3.0.2-22.el7.x86_64.rpm](http://rpmfind.net/linux/centos/7.4.1708/os/x86_64/Packages/vsftpd-3.0.2-22.el7.x86_64.rpm)

###
### **3、CentOS7yum安装出现/var/run/yum.pid 已被锁定，解决办法 ： **
root[@bogon](https://my.oschina.net/u/3914826) ~]# yum install -y gcc gcc+
已加载插件：fastestmirror, langpacks
/var/run/yum.pid 已被锁定，PID 为 11154 的另一个程序正在运行。
Another app is currently holding the yum lock; waiting for it to exit...
另一个应用程序是：PackageKit
内存：130 M RSS （1.4 GB VSZ）
已启动： Mon May  1 16:17:14 2017 - 04:06之前
状态  ：睡眠中，进程ID：11154
Another app is currently holding the yum lock; waiting for it to exit...
另一个应用程序是：PackageKit
内存：130 M RSS （1.4 GB VSZ）
已启动： Mon May  1 16:17:14 2017 - 04:08之前
状态  ：睡眠中，进程ID：11154

经过百度发现只要删除/var/run/yum.pid就可以正常使用了，即

rm -rf /var/run/yum.pid.
/sbin/service yum-updatesd restart

4、yum 安装软件时，报错：No package XXX available.

[root[@localhost](https://my.oschina.net/u/570656) ~]# yum -y install redis
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
* addons: mirrors.163.com
* base: mirrors.163.com
* extras: mirrors.163.com
* updates: mirrors.163.com
Setting up Install Process
No package redis available.
Nothing to do
解决方法：

1）.先去更新一下yum仓库：
#yum -y update

5、yum安装软件报错：curl#6 - "Could not resolve host: mirrorlist.centos.org; Temporary failure in name resolut

# yum install -y epel-release
Loaded plugins: fastestmirror
Repository base is listed more than once in the configuration
Repository updates is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=stock error was
14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Temporary failure in name resolution"

1. Contact the upstream for the repository and get them to fix the problem.

2. Reconfigure the baseurl/etc. for the repository, to point to a working
upstream. This is most often useful if you are using a newer
distribution release than is supported by the repository (and the
packages for the previous distribution release still work).

dns解析的问题，处理办法：
vim /etc/resolv.conf  加入：

nameserver 8.8.8.8
nameserver 8.8.4.4
search localdomain
