# nginx_exporter

nginx_exporter相关技术笔记。

nginx-dashboard-id=12708

## nginx_exporter-docker-compose
```shell
version: '3.0'
services:
  nginx_exporter:
    image: nginx/nginx-prometheus-exporter:0.11
    container_name: nginx_exporter
    hostname: nginx_exporter
    command:
     - '-nginx.scrape-uri=http://192.168.100.251/stub_status'
    restart: always
    ports:
    - "9113:9113"

```

## nginx-docker-compose
```yaml
version: "3.0"
services:
  nginx:
    image: nginx
    container_name: nginx
    ports:
     - 80:80
    volumes:
     - /root/nginx/conf.d/:/etc/nginx/conf.d/
     - /root/nginx/html:/usr/share/nginx/html
     - /root/nginx/log:/var/log/nginx

```

## server.conf
```nginx
server {
  listen       80;
  server_name  localhost;

  location /stub_status {
    stub_status on;
    access_log off;
    #allow nginx_exporter的ip;
    allow 0.0.0.0/0;
    deny all;

  }

  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
  }

  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    root   /usr/share/nginx/html;
  }
}

```

## 检查是否开启stub_status
```shell
curl 192.168.100.251/stub_status
#输出如下
Active connections: 1
server accepts handled requests
 1 1 1
Reading: 0 Writing: 1 Waiting: 0
```
