# 自签名证书https设置

自签名证书https设置相关技术笔记。

```bash
openssl genrsa -out ca.key 2048
openssl req -new -key ca.key -out nginx.csr
openssl x509 -req -days 3650  -in nginx.csr -signkey ca.key -out nginx.crt
```

将生成的私钥文件（server.key）和证书文件（server.crt）移动到适当的位置。例如，将它们移到 /etc/nginx/ssl/

+ Country Name (2 letter code) [AU]: 国家/地区名称的两个字母代码。例如，中国的代码是 "CN"，美国是 "US"。您可以输入适当的国家/地区代码，或者直接按回车键跳过。
+ State or Province Name (full name) [Some-State]: 州/省份名称的全名。您可以输入所在州或省的名称，或者直接按回车键跳过。
+ Locality Name (eg, city) []: 城市名称。请输入您所在的城市名称，或者按回车键跳过。
+ Organization Name (eg, company) [Internet Widgits Pty Ltd]: 组织名称，例如公司名称。您可以输入您的组织名称，或者直接按回车键跳过。
+ Organizational Unit Name (eg, section) []: 组织单位名称，例如部门名称。您可以输入您的部门名称，或者按回车键跳过。
+ Common Name (e.g. server FQDN or YOUR name) []: 通用名称，也称为主机名。这是您的服务器的完全限定域名 (FQDN) 或您的个人名称。如果您正在为特定域名的服务器生成证书，请输入该域名；如果您只是想生成一个通用的自签名证书，请输入您的名称或其他标识符。
+ Email Address []: 电子邮件地址。您可以输入您的电子邮件地址，或者按回车键跳过。

```bash
server {
    listen 80;
    server_name www.luobozi.top; #域名匹配

    location / {
        return 301 https://$host$request_uri;  # 将 HTTP 请求重定向到 HTTPS
    }
}

server {
    listen 443 ssl;
    server_name www.luobozi.top;
    ssl_certificate /etc/nginx/ssl/server.crt;  # 替换为您的证书文件路径
    ssl_certificate_key /etc/nginx/ssl/server.key;  # 替换为您的私钥文件路径
#这将告诉 Nginx 监听 443 端口，并使用指定的证书文件和私钥文件进行 HTTPS 加密通信。

    location / {
        proxy_pass http://127.0.0.1:8080; #代理到本地的8080端口差点
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
