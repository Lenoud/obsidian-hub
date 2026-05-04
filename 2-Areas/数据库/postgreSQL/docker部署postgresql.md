# Docker 部署 PostgreSQL

使用 Docker Compose 部署 PostgreSQL 14 数据库服务。

## 部署配置

```yaml
version: '3.8'

services:
  db:
    hostname: postgres
    container_name: postgres14
    image: postgres:14.12
    restart: always
    environment:
      POSTGRES_PASSWORD: 202127540226
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    network_mode: db_bridge
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| `image` | `postgres:14.12` | PostgreSQL 14 版本镜像 |
| `POSTGRES_PASSWORD` | 自定义 | 数据库密码 |
| `ports` | `5432:5432` | PostgreSQL 默认端口 |
| `volumes` | `./postgres_data` | 数据持久化目录 |

## 相关笔记
- [[MOC-Docker]]
- [[postgres]]
