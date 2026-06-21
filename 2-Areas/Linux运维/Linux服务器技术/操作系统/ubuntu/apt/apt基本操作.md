# apt 基本操作

Ubuntu / Debian 系统使用 apt(Advanced Package Tool)管理软件包,apt 是 apt-get / apt-cache 等命令的统一前端,语法更简洁、输出更友好。

## apt vs apt-get

| 命令 | 说明 |
| --- | --- |
| `apt` | 新版(2014+),用户友好,推荐日常使用 |
| `apt-get` | 老命令,适合脚本(输出稳定) |
| `apt-cache` | 老命令,查询包信息 |

两者底层都用 dpkg,装的包格式一样。

## 常用命令

### 更新与升级

```bash
# 更新软件包索引(必做,且先于 install/upgrade)
sudo apt update

# 升级所有已安装的包(不删除包)
sudo apt upgrade

# 整体升级(可删除旧依赖、安装新依赖,跨大版本升级用)
sudo apt full-upgrade

# 升级某个具体包
sudo apt install --only-upgrade nginx
```

### 查找

```bash
# 精确匹配某个包是否可安装
apt list --installed                 # 列出所有已安装
apt list --upgradable                # 列出可升级
apt list -a nginx                    # 查看所有版本

# 模糊查找包
apt search vim                       # 在包名和描述中搜
apt list | grep vim                  # 过滤已安装

# 查看包信息(依赖、大小、来源)
apt show nginx
```

### 安装

```bash
# 安装最新版
sudo apt install nginx

# 安装多个
sudo apt install nginx mysql-server php-fpm

# 安装指定版本
sudo apt install nginx=1.18.0-0ubuntu1

# 安装但不升级已装包
sudo apt install nginx --no-upgrade

# 修复损坏的依赖
sudo apt --fix-broken install
```

### 卸载

```bash
# remove:卸载包,但保留配置文件(/etc 下的)
sudo apt remove nginx

# purge:卸载包 + 删除配置文件
sudo apt purge nginx                # 或 apt-get purge

# 自动清理不再需要的依赖
sudo apt autoremove
sudo apt autoremove --purge         # 连配置一起清

# 清理下载的 .deb 缓存
sudo apt clean                      # 清 /var/cache/apt/archives/
sudo apt autoclean                  # 只清过期的
```

## 软件源(source list)

```bash
# 源配置文件
/etc/apt/sources.list
/etc/apt/sources.list.d/*.list      # 第三方 PPA / 自建源

# 添加 PPA(Personal Package Archive)
sudo add-apt-repository ppa:nginx/stable
sudo apt update

# 添加第三方源(传统方式)
sudo tee /etc/apt/sources.list.d/docker.list <<EOF
deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable
EOF

# 添加 GPG 密钥
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 或(新方式)
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
```

## 防止交互式询问(脚本场景)

```bash
# 设置非交互模式
sudo DEBIAN_FRONTEND=noninteractive apt install -y nginx

# 或全局配置
sudo apt install -y -o Dpkg::Options::="--force-confold" nginx
# --force-confold:遇到配置文件冲突,保留旧版本
```

## 锁文件冲突

```bash
# 报错:Could not get lock /var/lib/dpkg/lock-frontend
# 原因:另一个 apt 进程正在运行,等待即可

# 如果确认没 apt 在跑(异常退出残留)
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock*
sudo dpkg --configure -a
```

## 相关笔记
- [[查找已经安装的软件包]] — dpkg/apt 查询已装包
- [[ubuntu配置源]] — 换国内源
- [[卸载snap]] — 卸载 snap
- [[MOC-Linux运维]]
