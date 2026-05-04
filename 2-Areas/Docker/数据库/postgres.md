# PostgreSQL 14 数据库

功能强大的开源关系型数据库系统，以稳定性、扩展性和标准合规性著称。

## 部署

```yaml
version: "3.8"
services:
  db:
    hostname: postgres
    container_name: postgres14
    image: postgres:14.12
    restart: always
    environment:
      POSTGRES_PASSWORD: 000000 # 设置数据库密码
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./postgres-data:/var/lib/postgresql/data
    ports:
      - 5432:5432
    network_mode: db_bridge
```

## 说明

- 访问端口：`5432`
- `POSTGRES_PASSWORD`：数据库超级用户密码
- 数据持久化：`./postgres-data` 映射到 `/var/lib/postgresql/data`
- `network_mode: db_bridge`：使用自定义网络

## 相关笔记
- [[postgreSQL]]
- [[docker部署postgresql]]
- [[MOC-Docker]]
