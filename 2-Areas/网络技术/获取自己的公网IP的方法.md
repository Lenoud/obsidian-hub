# 获取自己的公网IP的方法

获取自己的公网IP的方法相关技术笔记。

以下为 linux 如何获取公网 ip 的方法

+ [curl cip.cc](https://www.cnblogs.com/newber/p/14178144.html#curl-cipcc)
+ [curl ifconfig.me](https://www.cnblogs.com/newber/p/14178144.html#curl-ifconfigme)

## curl cip.cc
+ linux 命令：curl cip.cc
+ windows 命令：telnet cip.cc
+ 纯 IP 命令：curl ip.cip.cc

```text
[root@localhost ~]# curl cip.cc
IP      : 115.196.88.33
地址    : 中国  北京
运营商  : 阿里云/联通/电信/移动

数据二  : 北京市 | 阿里云

数据三  : 中国北京北京 | 电信

URL     : http://www.cip.cc/115.196.88.33
```

```text
[root@localhost ~]# curl ip.cip.cc
116.196.112.113
```

## curl ifconfig.me
```text
[root@localhost ~]# curl ifconfig.me
116.196.112.113
```

使用get方法访问[https://ifconfig.me/ip](https://ifconfig.me/ip)即可获得一个纯ip地址

## 获取json格式的信息
[https://ifconfig.io/all.json](https://ifconfig.io/all.json)

[https://api.ipify.org/?format=json](https://api.ipify.org/?format=json)

[https://ip.useragentinfo.com/json](https://ip.useragentinfo.com/json)

[https://api.vvhan.com/api/getIpInfo](https://api.vvhan.com/api/getIpInfo)
