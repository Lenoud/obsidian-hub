# Docker 部署 WordPress

使用 Docker Compose 一键部署 WordPress + MySQL,适合开发测试、个人博客、小型网站。

> 参考文档:<https://www.yuque.com/u33971054/atri/foe7cuk01y2g5b1h>

## 部署方案

WordPress 是经典的 LAMP 应用,容器化后通常拆为:
- **WordPress 容器**:PHP + Nginx/Apache
- **MySQL 容器**:数据库(或 MariaDB)
- **(可选)Nginx 代理**:前面套一层 Nginx 做 TLS / 域名路由

## docker-compose.yml

```yaml
services:
  db:
    image: mysql:8.0
    container_name: wp-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_pass
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - wp_net

  wordpress:
    image: wordpress:php8.2-apache
    container_name: wp-app
    restart: always
    depends_on:
      - db
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wordpress
    ports:
      - 8080:80
    volumes:
      - wp_data:/var/www/html
    networks:
      - wp_net

volumes:
  db_data:
  wp_data:

networks:
  wp_net:
```

## 启动与初始化

```bash
# 启动
docker compose up -d

# 查看状态
docker compose ps
docker compose logs -f wordpress

# 访问安装向导
# 浏览器:http://<server_ip>:8080
# 按提示选择语言、设置站点标题、管理员账号密码
```

## 数据备份

```bash
# 1. 备份数据库
docker exec wp-db mysqldump -uroot -prootpass wordpress > backup-$(date +%F).sql

# 2. 备份 wp-content(上传文件、主题、插件)
docker cp wp-app:/var/www/html/wp-content ./wp-content-backup

# 3. 恢复数据库
docker exec -i wp-db mysql -uroot -prootpass wordpress < backup-2024-01-01.sql
```

## 升级 WordPress

```bash
# 拉取新镜像
docker compose pull

# 滚动重启(WordPress 容器会重新创建)
docker compose up -d

# 数据库无需升级(数据持久化在 volume)
```

## 生产环境建议

| 维度 | 开发版(上)| 生产版 |
| --- | --- | --- |
| 端口 | 8080 直连 | Nginx 代理 + TLS 443 |
| 数据库密码 | 明文写 yml | 用 Docker Secret 或 `.env` 文件 |
| 备份 | 手动 | 定时任务 + 上传到 OSS/S3 |
| WordPress 版本 | `latest` | 固定版本(如 `php8.2-apache`) |
| 插件/主题 | 直接装 | 用 wp-cli 容器预装 |
| 资源 | 默认 | 限制 `mem_limit: 512m` |

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 安装向导刷新后报"数据库连接错误" | db 容器还没启动完成 | `depends_on` 只保证启动顺序,不保证 ready;WordPress 会自动重试 |
| 上传大文件失败(413) | 默认上传限制 2M | 在 `uploads.ini` 里加 `upload_max_filesize = 64M` |
| 升级后页面 500 | 插件与新版本 WP 不兼容 | `docker exec` 进容器禁用插件 |
| 域名访问显示 IP | WordPress 数据库存了站点 URL | `wp option update siteurl https://new.domain` |
| 速度慢 | PHP-FPM 进程少 / 没缓存 | 加 Redis 缓存插件,用 fpm 版镜像 |

## 相关笔记
- [[wordpress]] — 另一个部署示例(可能更老)
- [[docker部署mysql]] — MySQL 容器化
- [[Nginx]] — 反向代理 + TLS
- [[MOC-Docker]]
- [[MOC-服务部署]]
