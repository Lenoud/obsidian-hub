# Docker CE 安装

Docker 社区版的安装指南，基于 CentOS/RHEL 系统，使用 yum 包管理器进行安装。

## 关闭防火墙

```bash
systemctl disable firewalld
systemctl stop firewalld
```

## yum 安装

```bash
yum install -y yum-utils
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo   # 中央仓库
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo   # 阿里仓库
#搜索版本
yum list docker-ce --showduplicates | sort -r
yum install -y docker-ce-27.3.1-1.el9
```

## 配置 daemon.json

```bash
cat >> /etc/docker/daemon.json << EOF
{
  "insecure-registries": ["0.0.0.0/0"],
  "registry-mirrors": ["https://docker.1panelproxy.com","https://proxy.1panel.live","https://dockerproxy.1panel.live"]
}
EOF
```

## 说明

- 安装版本：`docker-ce-27.3.1-1.el9`
- `insecure-registries`：允许所有地址的不安全镜像仓库
- `registry-mirrors`：配置国内镜像加速地址
