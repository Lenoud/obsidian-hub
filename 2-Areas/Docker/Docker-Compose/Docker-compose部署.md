# Docker-compose部署

Docker-compose部署相关技术笔记。

## 下载doker-compose
## 下载地址 [https://github.com/docker/compose/releases](https://github.com/docker/compose/releases)
下载名为docker-compose-linux-x86_64的文件

移动到PATH文件的目录下并改名为docker-compose

mv docker-compose-Linux-x86_64 /usr/local/bin/docker-compose

赋予可执行权限

chmod +x /usr/local/bin/docker-compose

测试：docker-compose -v

curl -SL [https://github.com/docker/compose/releases/download/v2.27.1/docker-compose-linux-x86_64](https://github.com/docker/compose/releases/download/v2.27.1/docker-compose-linux-x86_64) -o /usr/local/bin/docker-compose

官网：docker官方文档：https://docs.docker.com/compose/install/linux/
