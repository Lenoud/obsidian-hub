# SQL Server 2022 数据库

微软推出的关系型数据库管理系统，企业级数据平台，支持高性能事务处理和商业智能。

## 部署

```yaml
version: "3.8"
services:
  sqlserver2022:
    hostname: sqlserver
    container_name: sqlserver
    image: mcr.microsoft.com/mssql/server:2022-latest
    user: root
    restart: always
    environment:
      ACCEPT_EULA: Y
      SA_PASSWORD: 12345678.
      #SA 用户密码（至少包含8个字符，且需包含大写字母、小写字母、数字和特殊字符中的三种）
    ports:
      - 1433:1433
    volumes:
      - ./root:/root
      - ./mssql:/var/opt/mssql
      - /etc/localtime:/etc/localtime:ro
    network_mode: db_bridge
```

## 说明

- 访问端口：`1433`
- `ACCEPT_EULA`：接受 SQL Server 许可协议（必须设为 `Y`）
- `SA_PASSWORD`：SA 管理员密码（需包含大写、小写、数字和特殊字符中的三种，至少 8 位）
- 数据持久化：`./mssql` 映射到 `/var/opt/mssql`
- `network_mode: db_bridge`：使用自定义网络
