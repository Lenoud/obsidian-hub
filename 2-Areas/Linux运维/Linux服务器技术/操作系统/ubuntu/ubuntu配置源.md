# ubuntu配置源

Ubuntu 系统更换软件源配置。

### 1.默认安装好的ubuntu是国外的源，下载和更新软件比较慢！！！ 换到国内源可以加快更新
```bash
# 备份原来的源并且另存
sudo cp -v /etc/apt/sources.list /etc/apt/sources.list.backup #
# 执行chmod命令更改文件权限使软件源文件可编辑
sudo chmod 777 /etc/apt/sources.list
# 通过gedit命令编辑软件源：
sudo gedit /etc/apt/sources.list
# 执行上面命令后如果报sudo: gedit: command not found的错误，执行下面命令
sudo apt install vim
vim /etc/apt/sources.list

```

打开源文件后，按 i 进入编辑模式将所有的代码删干净，然后复制粘贴你需要的镜像源，先按ECS键退出编辑，然后使用 :wq 命令退出编辑模式

```bash
#换完源之后执行以下命令
sudo apt update
#更新源
sudo apt upgrade
#更新所有应用
#可复制如下的内容粘贴到sources.list中（复制其中一个源即可）
#阿里云国内源Ubuntu
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

#阿里云国内源Ubuntu（包括预发布的源）
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
#源码镜像
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

#2.清华大学源Ubuntu
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
```

```yaml
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://repo.huaweicloud.com/ubuntu jammy main restricted
# deb-src http://repo.huaweicloud.com/ubuntu jammy main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://repo.huaweicloud.com/ubuntu jammy-updates main restricted
# deb-src http://repo.huaweicloud.com/ubuntu jammy-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://repo.huaweicloud.com/ubuntu jammy universe
# deb-src http://repo.huaweicloud.com/ubuntu jammy universe
deb http://repo.huaweicloud.com/ubuntu jammy-updates universe
# deb-src http://repo.huaweicloud.com/ubuntu jammy-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team, and may not be under a free licence. Please satisfy yourself as to
## your rights to use the software. Also, please note that software in
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://repo.huaweicloud.com/ubuntu jammy multiverse
# deb-src http://repo.huaweicloud.com/ubuntu jammy multiverse
deb http://repo.huaweicloud.com/ubuntu jammy-updates multiverse
# deb-src http://repo.huaweicloud.com/ubuntu jammy-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://repo.huaweicloud.com/ubuntu jammy-backports main restricted universe multiverse
# deb-src http://repo.huaweicloud.com/ubuntu jammy-backports main restricted universe multiverse

deb http://repo.huaweicloud.com/ubuntu jammy-security main restricted
# deb-src http://repo.huaweicloud.com/ubuntu jammy-security main restricted
deb http://repo.huaweicloud.com/ubuntu jammy-security universe
# deb-src http://repo.huaweicloud.com/ubuntu jammy-security universe
deb http://repo.huaweicloud.com/ubuntu jammy-security multiverse
# deb-src http://repo.huaweicloud.com/ubuntu jammy-security multiverse

```

### 2.配置本地源
```bash
# 配置离线源
cat > /etc/apt/sources.list << EOF
deb [trusted=yes] file:// /opt/localsource/
EOF
#注意:
#packs后面有一个斜杠，全路径前面还要有空格

# 清空缓存
apt clean all

# 加载源
apt update
```

```python
# 默认注释了源码镜像以提高 apt update 速度
# 清华大学的软件源
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free

# 阿里云的软件源
# deb https://mirrors.aliyun.com/debian/ bullseye main non-free contrib
# deb-src https://mirrors.aliyun.com/debian/ bullseye main non-free contrib
# deb https://mirrors.aliyun.com/debian-security/ bullseye-security main
# deb-src https://mirrors.aliyun.com/debian-security/ bullseye-security main
# deb https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
# deb-src https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
# deb https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
# deb-src https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib

# 中科大的软件源
# deb https://mirrors.ustc.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.ustc.edu.cn/debian/ bullseye main contrib non-free
# deb https://mirrors.ustc.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.ustc.edu.cn/debian/ bullseye-updates main contrib non-free
# deb https://mirrors.ustc.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.ustc.edu.cn/debian/ bullseye-backports main contrib non-free
# deb https://mirrors.ustc.edu.cn/debian-security bullseye-security main contrib non-free
# deb-src https://mirrors.ustc.edu.cn/debian-security bullseye-security main contrib non-free
————————————————
版权声明：本文为CSDN博主「名字太长真的很奇怪꒰⑅•ᴗ•⑅꒱」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/bynever/article/details/126847421
```
