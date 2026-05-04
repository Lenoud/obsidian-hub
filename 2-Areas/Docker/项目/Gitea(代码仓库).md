# Gitea 代码仓库

轻量级自托管 Git 服务，提供 Web 界面进行代码管理、Pull Request、Issue 追踪等功能。

## 部署

需要提前准备好数据库。

```yaml
services:
  gitea:
    image: commitgo/gitea-ee:22.3.1
    container_name: gitea
    hostname: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=${DB_TYPE}
      - GITEA__database__HOST=${DB_HOST}:${DB_PORT}
      - GITEA__database__NAME=${DB_NAME}
      - GITEA__database__USER=${DB_USER}
      - GITEA__database__PASSWD=${DB_USER_PASSWORD}
    restart: always
      - jenkins_default
    volumes:
      - ./data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - ${PORT_HTTP}:3000
      - ${PORT_SSH}:22
networks:
  jenkins_default:
    driver: bridge
```

### .env 配置

```ini
DB_TYPE=mysql
DB_HOST=192.168.100.100
DB_PORT=3306
DB_NAME=gitea_data
DB_USER=root
DB_USER_PASSWORD=000000
PORT_HTTP=8081
PORT_SSH=222
```

## 说明

- Web 访问端口：通过 `PORT_HTTP` 环境变量配置（默认 `8081`）
- SSH 端口：通过 `PORT_SSH` 环境变量配置（默认 `222`）
- `USER_UID` / `USER_GID`：运行用户的 UID 和 GID
- 数据库连接通过环境变量配置：`DB_TYPE`、`DB_HOST`、`DB_PORT`、`DB_NAME`、`DB_USER`、`DB_USER_PASSWORD`
- 数据持久化：`./data` 映射到 `/data`
- 使用 `jenkins_default` 网络
