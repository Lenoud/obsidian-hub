# DataEase 数据可视化分析平台

开源的数据可视化分析工具，支持报表、仪表板和大屏展示，适用于数据分析和 BI 场景。

## 部署

```yaml
services:
  dataease:
    container_name: dataease
    image: registry.cn-qingdao.aliyuncs.com/dataease/dataease:v2.10.1
    network_mode: db_bridge
    ports:
      - 894:8100
    restart: always
    volumes:
      - ./conf:/opt/apps/config
      - ./logs:/opt/dataease2.0/logs
      - ./data/static-resource:/opt/dataease2.0/data/static-resource
      - ./cache:/opt/dataease2.0/cache
      - ./data/geo:/opt/dataease2.0/data/geo
      - ./data/exportData:/opt/dataease2.0/data/exportData
      - ./data/font:/opt/dataease2.0/data/font
    environment:
      PANEL_DB_HOST: mysql8
      PANEL_DB_NAME: dataease_db
      PANEL_DB_PORT: "3306"
      PANEL_DB_USER: dataease
      PANEL_DB_USER_PASSWORD: 000000
```

## 说明

- 访问地址：`http://<IP>:894`
- `PANEL_DB_HOST`：后端数据库主机名（需提前部署 MySQL）
- `PANEL_DB_NAME`：数据库名称
- `PANEL_DB_PORT`：数据库端口
- `PANEL_DB_USER`：数据库用户名
- `PANEL_DB_USER_PASSWORD`：数据库密码
- 端口映射：`894` -> `8100`
- 数据持久化：`./conf`、`./logs`、`./data`、`./cache`
