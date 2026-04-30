# WordPress 上云（LNMP 部署）

在鲲鹏云服务器上部署 LNMP + WordPress，使用云数据库的读写分离地址。

## 安装 LNMP

```bash
yum install -y php php-fpm php-mysql nginx-1.18.0
tar -zxvf /opt/wordpress-5.0.2-zh_CN.tar.gz -C /usr/share/nginx/
cd /usr/share/nginx/
chown -R nginx:nginx wordpress/
```

## 配置 Nginx

编辑 `/etc/nginx/conf.d/default.conf`：

```nginx
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/wordpress;
        index  index.php index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        root           /usr/share/nginx/wordpress;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

## 启动服务

```bash
systemctl enable nginx --now
systemctl enable php-fpm --now
```
