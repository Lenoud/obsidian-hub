# phpMyAdmin 数据库管理工具

基于 Web 的 MySQL/MariaDB 数据库管理工具，提供直观的图形化界面用于数据库操作。

## 部署

```yaml
version: "3.9"
services:
  phpmyadmin:
    hostname: phpmyadmin
    container_name: phpmyadmin
    deploy:
      resources:
        limits:
          cpus: "0"
    environment:
      PMA_ARBITRARY: "1"
      PMA_UPLOAD_DIR: /data/upload # 自定义上传目录路径
      PMA_SAVE_DIR: /data/save # 自定义保存目录路径
      MEMORY_LIMIT: 3G #将覆盖 phpMyAdmin 的内存限制默认为 512M
      UPLOAD_LIMIT: 10G #此选项将覆盖 Apache 和 PHP-FPM 的默认值默认值为 2048K
    image: phpmyadmin:5.2.1
    network_mode: db_bridge
    ports:
      - 888:80
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data/:/data/
```

## 说明

- 访问地址：`http://<IP>:888`
- 端口映射：`888` -> `80`
- `PMA_ARBITRARY`：设为 `1` 允许自由输入数据库服务器地址
- `PMA_UPLOAD_DIR`：自定义上传目录路径
- `PMA_SAVE_DIR`：自定义保存目录路径
- `MEMORY_LIMIT`：PHP 内存限制，覆盖默认 512M
- `UPLOAD_LIMIT`：上传文件大小限制，覆盖默认 2048K
- `network_mode: db_bridge`：使用自定义网络，确保与数据库容器在同一网络
