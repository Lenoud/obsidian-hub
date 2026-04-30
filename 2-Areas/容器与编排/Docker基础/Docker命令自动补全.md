# Docker 命令自动补全

Docker 命令行自动补全配置，安装 `bash-completion` 后即可使用 Tab 键补全 docker 和 docker-compose 子命令。

## 安装

```bash
# CentOS/RHEL
yum install -y bash-completion

# Ubuntu/Debian
apt install -y bash-completion
```

## 配置

```bash
# 如果 Docker 是包管理器安装的，补全脚本通常已自动注册
# 离线安装的 Docker 需要手动复制补全脚本

# 方法一：从 Docker 官方获取
curl -L https://raw.githubusercontent.com/docker/docker-ce/master/components/cli/contrib/completion/bash/docker -o /etc/bash_completion.d/docker

# 方法二：从已安装的 Docker 中查找
find / -name "docker.bash" 2>/dev/null
find / -name "docker-compose.bash" 2>/dev/null

# 使补全生效
source /etc/bash_completion.d/docker
```

## 说明

| 工具 | 补全脚本位置 |
| :--- | :--- |
| docker | `/usr/share/bash-completion/completions/docker` |
| docker-compose | `/usr/share/bash-completion/completions/docker-compose` |
