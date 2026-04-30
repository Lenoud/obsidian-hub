# haproxy负载均衡器

haproxy负载均衡器相关技术笔记。

```bash
version: "3"
services:
  haproxy:
    image: haproxy:1.7
    container_name: haproxy-service
    network_mode: rabbitmq-cluster_rabbitmq_net
    ports:
      - "5672:5672"
    volumes:
      - ./haproxy_service/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg
    restart: always
```

```bash
#./haproxy_service/haproxy.cfg
defaults
    log global
    mode tcp
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout connect 30s                  # 设置连接超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒

frontend rabbitmq_frontend
    bind *:5672                          # 监听所有IP地址的5672端口
    default_backend rabbitmq_backend     # 默认转发到rabbitmq_backend后端

backend rabbitmq_backend
    mode tcp
    balance roundrobin                   # 使用轮询算法进行负载均衡
    timeout client 30s                   # 设置客户端超时时间为30秒
    timeout server 30s                   # 设置服务器超时时间为30秒
    server rabbitmq1 rabbitmq1:5672 check  # 定义rabbitmq1服务器，使用5672端口，并进行健康检查
    server rabbitmq2 rabbitmq2:5672 check  # 定义rabbitmq2服务器，使用5672端口，并进行健康检查
    server rabbitmq3 rabbitmq3:5672 check  # 定义rabbitmq3服务器，使用5672端口，并进行健康检查

```

```bash
global					//全局参数的设置
    log         127.0.0.1 local2	// 全局的日志配置，使用log关键字，指定使用127.0.0.1上的syslog服务中的local2日志设备
    chroot      /var/lib/haproxy	//改变当前工作目录
    pidfile     /var/run/haproxy.pid	//当前进程id文件
    maxconn     4000			//最大连接数
    user        haproxy			//所属用户
    group       haproxy			//所属组
    daemon    				//以守护进程方式运行haproxy
    stats socket /var/lib/haproxy/stats //基于本地的文件传输

defaults				//默认部分的定义
    mode                    http	//mode语法：mode {http|tcp|health} 。http是七层模式，tcp是四层模式，health是健康检测，返回OK
    log                     global	//应用全局的日志配置
    option                  httplog	//启用日志记录HTTP请求，默认haproxy日志记录是不记录HTTP请求日志
    option                  dontlognull	//启用该项，日志中将不会记录空连接。所谓空连接就是在上游的负载均衡器或者监控系统为了探测该服务是否存活可用时，需要定期的连接或者获取某一固定的组件或页面，或者探测扫描端口是否在监听或开放等动作被称为空连接；官方文档中标注，如果该服务上游没有其他的负载均衡器的话，建议不要使用该参数，因为互联网上的恶意扫描或其他动作就不会被记录下来
    option http-server-close		//每次请求完毕后主动关闭http通道
    option forwardfor       except 127.0.0.0/8	//如果服务器上的应用程序想记录发起请求的客户端的IP地址，需要在HAProxy上配置此选项， 这样 HAProxy会把客户端的IP信息发送给服务器，在HTTP请求中添加"X-Forwarded-For"字段。启用X-Forwarded-For，在requests头部插入客户端IP发送给后端的server，使后端server获取到客户端的真实IP。
    option                  redispatch
    retries                 3		//定义连接后端服务器的失败重连次数，连接失败次数超过此值后将会将对应后端服务器标记为不可用
    timeout http-request    10s		//http请求超时时间
    timeout queue           1m		//一个请求在队列里的超时时间
    timeout connect         10s		//连接超时
    timeout client          1m		//客户端超时
    timeout server          1m		//服务端超时
    timeout http-keep-alive 10s		//设置http-keep-alive的超时时间
    timeout check           10s		//检测超时
    maxconn                 3000	//每个进程可用的最大连接数

frontend  main *:5000			//设置监听地址，默认为5000
    acl url_static       path_beg       -i /static /images /javascript /stylesheets	//定义一个名为ur1_static的acl，当请求的url开头是以/static /images /javascript /stylesheets开头的，
    acl url_static       path_end       -i .jpg .gif .png .css .js			//请求的url末尾是以.css、.jpg、.png、.jpeg、.js、.gif结尾的，将会被匹配到

    use_backend static          if url_static	//如果满足策略url_static时，就将请求交予backend static
    default_backend             app		//否则，就将请求交予backend app

backend static					//定义一个名为static的后端部分,使用了静态动态分离（如果url_static匹配 .jpg .gif .png .css .js等静态文件则访问此后端
    balance     roundrobin			//设置负载均衡算法[banlance roundrobin 轮询；balance source 保存session值；支持static-rr（权重），leastconn，first，uri等参数]
    server      static 127.0.0.1:4331 check	//通过IP和端口，设置静态文件部署在IP所在的主机，check表示接受健康检测

backend app					//定义一个名为app的后端部分。PS：此处app只是一个自定义名字而已，但是需要与frontend里面配置项default_backend 值相一致
    balance     roundrobin			//负载均衡算法
    server  app1 127.0.0.1:5001 check		//使用server关键字来设置后端服务器,app1/2/3/4无特殊含义
    server  app2 127.0.0.1:5002 check
    server  app3 127.0.0.1:5003 check
    server  app4 127.0.0.1:5004 check
```
