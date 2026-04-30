# OpenStack安全策略提升实操手册
## 4.实战案例——OpenStack安全策略提升
### 4.1 案例目标
（1）了解https与http协议的区别

（2）了解如何将http协议提升至https协议

### 4.2 案例分析
#### 1.规划节点
OpenStack安全策略提升的节点规划，见表4-1-1。

表4-1-1 节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 172.33.16.10 | controller | OpenStack控制节点 |
| 172.33.16.20 | compute | OpenStack计算节点 |

#### 2.基础准备
使用OpenStack平台的两台节点进行实验。节点规划表中的IP地址为作者的IP地址，在进行实操案例的时候，按照自己的环境规划网络与IP地址。

### 4.3 案例实施
#### 1.了解http与https协议
##### （1）http协议简介
HTTP协议是Hyper Text Transfer Protocol（超文本传输协议）的缩写,是用于从万维网（WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。

HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

HTTP是一个属于应用层的面向对象的协议，由于其简捷、快速的方式，适用于分布式超媒体信息系统。它于1990年提出，经过几年的使用与发展，得到不断地完善和扩展。目前在WWW中使用的是HTTP/1.0的第六版，HTTP/1.1的规范化工作正在进行之中，而且HTTP-NG(Next Generation of HTTP)的建议已经提出。

HTTP协议工作于客户端-服务端架构为上。浏览器作为HTTP客户端通过URL向HTTP服务端即WEB服务器发送所有请求。Web服务器根据接收到的请求后，向客户端发送响应信息。

##### （2）https协议简介
HTTPS（全称：Hypertext Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。HTTPS协议 = HTTP协议 + SSL/TLS协议，在HTTPS数据传输的过程中，需要用SSL/TLS对数据进行加密和解密，需要用HTTP对加密后的数据进行传输，由此可以看出HTTPS是由HTTP和SSL/TLS一起合作完成的。

SSL的全称是Secure Sockets Layer，即安全套接层协议，是为网络通信提供安全及数据完整性的一种安全协议。SSL协议在1994年被Netscape发明，后来各个浏览器均支持SSL，其最新的版本是3.0

TLS的全称是Transport Layer Security，即安全传输层协议，最新版本的TLS（Transport Layer Security，传输层安全协议）是IETF（Internet Engineering Task Force，Internet工程任务组）制定的一种新的协议，它建立在SSL 3.0协议规范之上，是SSL 3.0的后续版本。在TLS与SSL3.0之间存在着显著的差别，主要是它们所支持的加密算法不同，所以TLS与SSL3.0不能互操作。虽然TLS与SSL3.0在加密算法上不同，但是在我们理解HTTPS的过程中，我们可以把SSL和TLS看做是同一个协议。

HTTPS为了兼顾安全与效率，同时使用了对称加密和非对称加密。数据是被对称加密传输的，对称加密过程需要客户端的一个密钥，为了确保能把该密钥安全传输到服务器端，采用非对称加密对该密钥进行加密传输，总的来说，对数据进行对称加密，对称加密所要使用的密钥通过非对称加密传输。

##### （3）http与https协议的对比
| **http** | VS | **https** |
| --- | --- | :---: |
| 明文传输，网站或相关服务与用户之间的数据交互无加密，极易被监听，破解甚至篡改。 | 传输方式 | 在 HTTP 下加入了 SSL 层，是数据传输变成加密模式，从而保护了交换数据隐私和完整性，简单来说它就是安全版的 HTTP。 |
| 无任何身份认证，用户无法通过http辨认出网站的真实身份。 | 身份认证 | 经过CA多重认证，包括域名管理权限认证，单位身份合法性确认等。EV证书甚至可以直接在浏览器地址栏显示单位名称，提升用户体验。 |
| 无任何使用成本，所有网站默认即 http 模式。 | 实现成本 | 需要申请SSL证书来实现https，价格几百元到上万元不等，详见：[SSL证书选购](https://www.sslzhengshu.com/guide/index.html) |
| 80端口 | 连接端口 | 443端口 |
| 提示网站不安全：
![[1699015721122-ceba2354-95c6-43c4-87ea-1785fcbaf326.png]]
当您的网站上有类似注册登陆等表单时，用户一旦进行输入，Chrome浏览器便红色高亮显示“不安全”：
   ![[1699015721432-3b929127-276e-479e-b826-b570a709676a.png]] | 浏览器
兼容性 | 提示网站连接是安全的：
![[1699015721996-4a910907-5fde-4f4d-82e0-7921bf3c3e60.jpeg]]
当您申请的是“EVSSL证书”时，浏览器地址栏会直接显示您的单位名称，可显著提升网站用户的信任度和单位形象：
![[1699015722366-943780ec-f8f7-4fd0-8854-98fb04c2b915.jpeg]] |
| 对http网站无任何优待。 | SEO优化 | 百度谷歌等官方声明提高 https 网站的排名权重。 |
| 极易被黑客或者恶意的同行进行流量劫持。 | 网站劫持 | 隐私信息加密，防止流量劫持 |
| 访问速度根据网站服务器配置和客户端的浏览环境而定。 | 访问速度 | 在同样配置的服务器以及客户端浏览环境下，可明显提高网页加载速度。 |
| 当网站需要与第三方平台进行对接时，通常不接受http这种连接。例如微信小程序，苹果ATS，抖音上做广告等等。 | 数据对接 | 微信小程序，抖音，苹果等等越来越多的平台只接受https这种加密的安全链接。 |
| 经常因为没有https,而被各个浏览器或者其他平台显示风险警告，导致损害用户的信任度。 | 网站形象 | 为网站的用户营造安全的浏览环境，是网站的基本责任，也是赢得用户信赖的一个重要因素。 |
| 无任何风险保障，当网站数据传输被截取导致重大损失时，只有网站运营者自己承担。 | 风险保障 | 拥有10万-175万美元的商业保险，当网站数据传输被破解时，有巨额的保障额度。 |
| 放弃不安全的 http | 您的选择 | [立即选购 https 证书](https://www.ihuandu.com/" \t "https://www.ihuandu.com/help/zsk/_self) |

#### 2.配置https协议
##### （1）配置yum源
使用远程连接工具连接到控制节点，移除本地repo文件，命令如下：

# mv /etc/yum.repos.d/* /media

使用提供的https-repo.tar.gz文件上传至controller节点的/root目录下，进行解压，并放到/opt目录下，命令如下：

# tar -zxvf https-repo.tar.gz -C /opt

创建repo文件，在/etc/yum.repos.d目录下创建ssl.repo文件：

# vi /etc/yum.repos.d/ssl.repo

# cat /etc/yum.repos.d/ssl.repo

[ssl]

name=ssl

baseurl=file:///opt/https-repo

gpgcheck=0

enabled=1

##### （2）安装服务
安装httpd、mod_wsgi、mod_ssl这几个组件，命令如下：

# yum install -y mod_wsgi httpd mod_ssl

... ...

Installed:

  mod_ssl.x86_64 1:2.4.6-93.el7.centos

Updated:

  httpd.x86_64 0:2.4.6-93.el7.centos

Dependency Updated:

  httpd-tools.x86_64 0:2.4.6-93.el7.centos

Complete!

##### （3）修改ssl配置文件
修改/etc/httpd/conf.d/ssl.conf配置文件，具体修改内容如下：

#SSLProtocol all -SSLv2 -SSLv3    //找到该行，并注释

SSLProtocol all -SSLv2            //添加该行

##### （4）开启SSL
配置/etc/openstack-dashboard/local_settings 配置文件，修改参数如下所示：

CSRF_COOKIE_SECURE = True             //将该行的注释取消

SESSION_COOKIE_SECURE = True          //将该行的注释取消

USE_SSL = True                           //添加该行

SESSION_COOKIE_HTTPONLY = True       //添加该行

##### （5）重启服务
在修改完上述的配置文件后，重启httpd服务和缓存服务，命令如下：

[root@controller ~]# service httpd restart

Redirecting to /bin/systemctl restart httpd.service

[root@controller ~]# service memcached restart

Redirecting to /bin/systemctl restart memcached.service

##### （6）访问dashboard
输入IP地址进行访问：[https://172.33.16.10/dashboard](https://172.33.16.10/dashboard)，通过https访问dashboard管理平台，如下图所示：

![[1699015722731-d72a5219-703f-4cd3-8752-265b07dabc1e.png]]

使用https协议访问界面，会出现不是私密连接的提示，单击“隐藏详情”按钮，然后单击“继续前往172.33.16.10（不安全）”连接，进行访问OpenStack的Dashboard界面，如下图所示：

![[1699015723233-184f33ed-29d4-46e6-b5fd-465b42b0cfa6.png]]

输入Domain、用户名和密码进行登录，成功登录后如下所示：

![[1699015723586-1e4c2126-0e15-4060-bd03-e775b12c5eb8.png]]

若继续使用http协议访问OpenStack的Dashboard界面，开会可以访问登录界面，但是在输入用户名和密码之后，会出现报错，如下图所示：

![[1699015723871-50246208-f3c3-4824-8b73-e1080ef9fed5.png]]

至此，将OpenStack的Dashboard访问策略提升至https成功。
