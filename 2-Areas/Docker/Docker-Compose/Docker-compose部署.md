# Docker-compose部署

Docker-compose部署相关技术笔记。

## 下载 docker-compose

下载地址：<https://github.com/docker/compose/releases>，下载名为 `docker-compose-linux-x86_64` 的文件。

移动到 PATH 目录下并改名为 `docker-compose`：

```bash
mv docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
```

赋予可执行权限：

```bash
chmod +x /usr/local/bin/docker-compose
```

测试：

```bash
docker-compose -v
```

或使用官方一键脚本下载：

```bash
curl -SL https://github.com/docker/compose/releases/download/v2.27.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

> 官方文档：<https://docs.docker.com/compose/install/linux/>

## 相关笔记
- [[Docker搭建]]
- [[dockerfile]]
- [[MOC-Docker]]
