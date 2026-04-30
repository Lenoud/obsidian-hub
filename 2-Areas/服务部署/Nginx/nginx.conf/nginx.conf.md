# nginx.conf

nginx.conf相关技术笔记。

```nginx
#this is nginx config file
user  nginx;
worker_processes  auto;
#worker的个数 自动一般按照cpu核心数来

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

###########################################################
#声明需要反向代理的服务器地址
upstream goweb {
  #server 172.23.0.2:8001 weight=3;weight=权重 越大分配请求越多
  server 172.23.0.2:8001;
  server 127.0.0.1:8000;
}
#反向代理goweb
server{
    listen       9999;
    server_name  localhost;
    #location /goweb  curl 127.0.0.1:9999/goweb
    location / {
      proxy_pass http://goweb; #和上面upstream goweb 的goweb保持一致
    }
}
# curl 127.0.0.1:9999即可反向代理访问到 127.0.0.1:8000和172.23.0.2:8001
###########################################################

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    #nginx中的server可以写到其它的目录下，通过上面这条命令声明
    #随后我们可以在/etc/nginx/conf.d中编写test.conf文件来配置我们的web服务
}

```

```bash
#user  nobody;
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen 8095;
        server_name localhost;
        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html;
        }
        location /api/ {
            rewrite ^/api/(.*)$ /$1 break;
            proxy_pass http://192.168.100.20:9998;
        }
    }
```

这是一个Nginx配置文件的示例，它描述了如何配置一个简单的Nginx服务器来处理HTTP请求。下面是配置文件的各个部分的解释：

user nobody;：这一行指定Nginx工作进程的运行用户。在这里，Nginx将以 nobody 用户的身份运行，这是一个常见的做法，以提高安全性。

worker_processes 1;：这一行指定Nginx启动的工作进程数量。在这里，只有一个工作进程。通常，Nginx可以配置多个工作进程以充分利用多核处理器，但在这个示例中，只有一个工作进程。

events 部分：

worker_connections 1024;：这里设置了每个工作进程可以处理的并发连接数。在这个示例中，每个工作进程可以处理最多 1024 个并发连接。

http 部分：

include mime.types;：这里包含了一个名为 mime.types 的文件，它定义了 MIME 类型和文件扩展名之间的映射关系，用于确定如何处理不同类型的文件。

default_type application/octet-stream;：如果无法确定文件的 MIME 类型，将使用 application/octet-stream 作为默认类型。

sendfile on;：启用了文件传输机制，可以提高文件传输的效率。

keepalive_timeout 65;：定义了客户端连接的超时时间，如果客户端在一段时间内没有活动，则将其断开连接。

server 部分：

listen 8095;：指定Nginx监听的端口号是 8095。

server_name localhost;：指定服务器的名称为 "localhost"。

location / 部分：处理根目录的请求。

root /usr/share/nginx/html;：指定服务器根目录为 /usr/share/nginx/html，这是存放网页文件的目录。

try_files $uri $uri/ /index.html;：如果请求的文件不存在，将尝试返回 /index.html。

location /api/ 部分：处理以 /api/ 开头的请求。

rewrite ^/api/(.*)$ /$1 break;：使用正则表达式将 /api/ 部分从请求中移除，然后将请求传递给下游服务器。

proxy_pass http://192.168.100.20:9998;：将请求代理到指定的下游服务器，其中 192.168.100.20:9998 是代理目标的地址和端口。

这个配置文件的主要作用是配置一个监听端口为 8095 的Nginx服务器，处理来自客户端的HTTP请求。根目录的请求会在 /usr/share/nginx/html 目录下查找文件，而以 /api/ 开头的请求会被代理到 192.168.100.20:9998 这个后端服务器上。
