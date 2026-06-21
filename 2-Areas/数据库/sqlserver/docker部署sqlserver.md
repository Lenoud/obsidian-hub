# Docker 部署 SQL Server

使用 Docker Compose 部署 Microsoft SQL Server 2017 和 2022 版本。

## SQL Server 2017

```yaml
version: '3'
services:
  sqlserver2017:
    hostname: sqlserver2017
    container_name: sqlserver2017
    image: mcr.microsoft.com/mssql/server:2017-latest
    restart: always
    environment:
      ACCEPT_EULA: Y
      SA_PASSWORD: 12345678.
    ports:
      - "1433:1433"
    volumes:
      - ./root/:/root
      - ./mssql_data:/var/opt/mssql
      - /etc/localtime:/etc/localtime:ro
```

## SQL Server 2022

```yaml
version: '3.8'
services:
  sqlserver2022:
    hostname: sqlserver2022
    container_name: sqlserver2022
    image: mcr.microsoft.com/mssql/server:2022-latest
    restart: always
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: "12345678."
    ports:
      - "1433:1433"
    volumes:
      - ./root:/root
      - ./mssql_data:/var/opt/mssql
      - /etc/localtime:/etc/localtime:ro
    networks:
      - sqlserver2022

networks:
  sqlserver2022:
    name: sqlserver2022
```

## 权限问题处理

如容器未能成功启动，通常是挂载目录权限问题：

```bash
sudo chown -R 10001:0 /root/sqlserver2022
sudo chmod -R g+rwx /root/sqlserver2022
```

## 说明

| 参数 | 说明 |
| --- | --- |
| `ACCEPT_EULA` | 必须设为 `Y` 接受许可协议 |
| `SA_PASSWORD` | SA 管理员密码（需含大小写字母、数字和特殊字符） |
| `ports` | `1433:1433`，SQL Server 默认端口 |

## 相关笔记
- [[sqlserver]]
- [[MOC-Docker]]
- [[MOC-数据库]]
