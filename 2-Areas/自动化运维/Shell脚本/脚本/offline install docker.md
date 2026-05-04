# offline install docker

离线安装 Docker 的 Shell 脚本。

```bash
#!/bin/bash

# 检查 docker-compose 文件是否存在
if [ ! -f /root/docker-compose-linux-x86_64 ]; then
    echo "docker-compose 文件不存在"
    exit 1
fi

# 复制 docker-compose 到 /usr/local/bin 并赋予执行权限
cp /root/docker-compose-linux-x86_64 /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 检查 docker.service 文件是否存在
if [ ! -f /root/docker.service ]; then
    echo "docker.service 文件不存在"
    exit 1
fi

# 复制 docker.service 文件到 systemd 目录
cp -rvf /root/docker.service /usr/lib/systemd/system/

# 检查 tar 文件是否存在
if [ ! -f /root/docker-27.0.3.tgz ]; then
    echo "docker-27.0.3.tgz 文件不存在"
    exit 1
fi

# 解压 docker-27.0.3.tgz 到 /root/
tar -zxvf /root/docker-27.0.3.tgz -C /root/

# 复制解压后的 docker 二进制文件到 /usr/local/bin/
cp -rvf /root/docker/* /usr/local/bin/

# 创建 /etc/docker 目录，如果不存在的话
if [ ! -d /etc/docker ]; then
    mkdir /etc/docker
fi

# 检查 daemon.json 文件是否存在
if [ ! -f daemon.json ]; then
    echo "daemon.json 文件不存在"
    exit 1
fi

# 复制 daemon.json 到 /etc/docker/
cp daemon.json /etc/docker/

# 重新加载 systemd 配置
systemctl daemon-reload

# 启动 Docker 服务
echo "启动 docker"
if ! systemctl start docker; then
    echo "启动 docker 失败"
    exit 1
fi

# 显示 Docker 信息
echo "查看 docker 信息"
if ! docker info; then
    echo "获取 docker 信息失败"
    exit 1
fi

```

## 相关笔记

- [[Docker搭建]]
- [[docker daemon.json配置]]
- [[MOC-Docker]]
