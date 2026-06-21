# Alist 网盘服务

Alist 是一个开源的网盘文件列表程序,支持将阿里云盘、百度网盘、OneDrive、Google Drive、本地存储等多种存储统一挂载为 Web 文件管理界面,支持 WebDAV。

- 官网:<https://alist.nn.ci/zh/>
- GitHub:<https://github.com/alist-org/alist>

## Docker 部署(推荐)

```bash
docker run -d \
  --restart=always \
  --name="alist" \
  -p 5244:5244 \
  -v /opt/alist/filedata:/opt/filedata \
  -v /opt/alist/config:/opt/alist/data \
  -e PUID=0 -e PGID=0 -e UMASK=022 \
  xhofe/alist:latest
```

环境变量说明:

| 变量 | 说明 |
| --- | --- |
| `PUID` | 运行用户的 UID(0 = root) |
| `PGID` | 运行用户的 GID(0 = root) |
| `UMASK` | 文件权限掩码(022 = 默认 755/644) |

## Docker Compose 部署

```yaml
# docker-compose.yml
services:
  alist:
    image: xhofe/alist:latest
    container_name: alist
    restart: always
    ports:
      - "5244:5244"
    environment:
      - PUID=0
      - PGID=0
      - UMASK=022
    volumes:
      - ./filedata:/opt/filedata        # 共享数据存放路径
      - ./config:/opt/alist/data        # 配置文件存放路径
      # - /mnt/nas:/mnt/nas             # 挂载本地其他路径
```

```bash
# 启动
docker compose up -d

# 查看状态
docker compose ps

# 查看日志
docker compose logs -f alist

# 检查 compose 文件语法
docker compose config
```

## 获取管理员密码

首次启动后,需要查看随机生成的管理员密码:

```bash
# Docker
docker exec -it alist ./alist admin

# Docker Compose
docker compose exec alist ./alist admin
# 输出:
#   username: admin
#   password: <随机密码>

# 手动设置新密码(3.25.0+)
docker exec -it alist ./alist admin set NEW_PASSWORD
```

浏览器访问 `http://<server-ip>:5244/`,用 admin / 密码登录。

## 版本查看

```bash
docker exec -it alist ./alist version
```

## 升级

```bash
# Docker
docker stop alist
docker rm alist
docker pull xhofe/alist:latest
# 重新执行 docker run(数据在 volume 里,不丢失)

# Docker Compose
docker compose pull
docker compose up -d
```

## 常见问题

| 问题 | 解决 |
| --- | --- |
| 忘记 admin 密码 | `docker exec -it alist ./alist admin set newpass` |
| 挂载网盘 401 | 网盘 token 过期,登录 Alist 管理界面重新授权 |
| 上传大文件失败 | Nginx 反代时加 `client_max_body_size 0;` |
| WebDAV 路径 | `http://<ip>:5244/dav/`,用户名密码同 Alist |
| 速度慢 | 检查网盘服务商限速,Alist 本身不限速 |

## 相关笔记
- [[MOC-服务部署]]
- [[docker-compose]]
- [[docker部署nginx]] — 反向代理 + TLS
