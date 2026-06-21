# Docker搭建

Docker搭建相关技术笔记。



## yum安装
1. [官网地址](https://www.docker.com/)
2. [公共仓库](https://hub.docker.com/)
3. [安装文档](https://docs.docker.com/get-docker/)

```bash
#1.先卸载低版本的docker
yum remove docker*

#CentOS 7（使用 yum 进行安装）

# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
#安装需要的软件包， yum-util 提供yum-config-manager功能，另两个是devicemapper驱动依赖

# Step 2: 添加软件源信息(下面两个任选一个)
# 官方源:
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 阿里云源(国内推荐):
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# Step 3: 更新并安装 Docker CE
sudo yum makecache fast
yum -y install docker-ce            # 安装最新版
# 或安装指定版本(查看可用版本:yum list docker-ce --showduplicates | sort -r)
# yum -y install docker-ce-24.0.7 docker-ce-cli-24.0.7 containerd.io
# Step 4: 开启 Docker 服务(开机自启 + 立即启动)
systemctl enable docker --now

# 注意:
# 官方软件源默认启用了最新的软件,可以通过编辑软件源获取各个版本的软件包。
# vim /etc/yum.repos.d/docker-ce.repo
#   将 [docker-ce-test] 下方的 enabled=0 修改为 enabled=1
#
# 安装指定版本:
# yum list docker-ce --showduplicates | sort -r       # 查看可用版本
# yum -y install docker-ce-[VERSION]                  # 安装指定版本

#5.配置阿里云镜像加速  #登录阿里云开发者平台-点击控制台-选择容器镜像服务-获取加速器地址
mkdir -p /etc/docker
cat >> /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.1ms.run",
    "https://docker-0.unsee.tech",
    "https://docker.hlmirror.com",
    "https://func.ink"
  ]
}

EOF
```

## docker离线安装
```bash
#docker离线安装
#下载网址 ：https://download.docker.com/linux/static/stable/x86_64/docker-20.10.10.tgz
curl -O https://download.docker.com/linux/static/stable/x86_64/docker-20.10.10.tgz
#解压docker
tar -xzvf docker-20.10.10.tgz -C /opt
#复制可执行文件到PATH目录下
cp docker/* /usr/bin/

#编辑配置文件
cat >> /etc/systemd/system/docker.service <<EOF
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/local/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target

EOF

#写入镜像加速
mkdir /etc/docker/
cat >> /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.1ms.run",
    "https://docker-0.unsee.tech",
    "https://docker.hlmirror.com",
    "https://func.ink"
  ]
}
EOF

#开启docker
systemctl daemon-reload
systemctl enable docker.service
systemctl start docker

```

```ini
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-debuginfo]
name=Docker CE Stable - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-source]
name=Docker CE Stable - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test]
name=Docker CE Test - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-debuginfo]
name=Docker CE Test - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-source]
name=Docker CE Test - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly]
name=Docker CE Nightly - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-debuginfo]
name=Docker CE Nightly - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/debug-$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-source]
name=Docker CE Nightly - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/$releasever/source/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

```
