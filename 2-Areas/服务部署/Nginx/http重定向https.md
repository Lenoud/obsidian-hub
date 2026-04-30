# http重定向https

http重定向https相关技术笔记。

```nginx
##################demo-2048
server {
    listen 80;
    server_name www.luobozi.top;

    location / {
        return 301 https://$host$request_uri;  # 将 HTTP 请求重定向到 HTTPS
    }
}

server {
    listen 443 ssl;
    server_name www.luobozi.top;

    ssl_certificate /etc/nginx/ssl/luobozi.top.pem;  # 证书文件路径
    ssl_certificate_key /etc/nginx/ssl/luobozi.top.key;  # 私钥文件路径

#ssl_protocols：指定允许使用的 SSL/TLS 协议版本，这里只允许使用 TLSv1.1  TLS 1.2 和 TLS 1.3。
#ssl_ciphers：配置 SSL 加密套件，这里指定了一组安全的加密算法。
#ssl_session_cache 指令定义了SSL会话缓存的类型和大小，ssl_session_timeout 指令定义了缓存中SSL会话的超时时间
    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://127.0.0.1:8005;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

################nps内穿仪表盘
server {
    listen 80;
    server_name nps.luobozi.top;

    location / {
        return 301 https://$host$request_uri;  # 将 HTTP 请求重定向到 HTTPS
    }
}

server {
    listen 443 ssl;
    server_name nps.luobozi.top;

    ssl_certificate /etc/nginx/ssl/luobozi.top.pem;
    ssl_certificate_key /etc/nginx/ssl/luobozi.top.key;
    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://127.0.0.1:9090;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

################weavescope服务
server {
    listen 80;
    server_name demo.luobozi.top;

    location / {
        return 301 https://$host$request_uri;  # 将 HTTP 请求重定向到 HTTPS
    }
}

server {
    listen 443 ssl;
    server_name demo.luobozi.top;

    ssl_certificate /etc/nginx/ssl/luobozi.top.pem;  # 替换为您的证书文件路径
    ssl_certificate_key /etc/nginx/ssl/luobozi.top.key;  # 替换为您的私钥文件路径
    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://127.0.0.1:4040;  #weavescope

#需要在 Nginx 上配置反向代理来处理 WebSocket 连接。对于 WebSocket，Nginx 需要使用特殊的配置来转发这些连接。
#设置了必要的头部信息以支持 WebSocket 连接。
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;

    }

    error_page   500 502 503 504  /50x.html; #50系列报错定向
    location = /50x.html {
        root   /usr/share/nginx/html;  }

}

#############宝塔服务
server {
    listen 80;
    server_name baota.luobozi.top;

    location / {
        return 301 https://$host$request_uri;  #重定向
    }
}

server {
    listen 443 ssl;
    server_name baota.luobozi.top;

    ssl_certificate /etc/nginx/ssl/luobozi.top.pem;  #证书文件路径
    ssl_certificate_key /etc/nginx/ssl/luobozi.top.key;  #私钥文件路径
    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://127.0.0.1:9003; #宝塔
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
#        limit_rate 256m;        # 用户下载限速
#        client_max_body_size 0; # 允许上传的文件大小无限制
#        #client_max_body_size 5G;   # 允许上传的文件5G

    }
}

```
