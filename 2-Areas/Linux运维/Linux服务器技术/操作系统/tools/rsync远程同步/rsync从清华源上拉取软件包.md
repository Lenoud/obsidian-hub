# rsync从清华源上拉取软件包

使用 rsync 从清华源拉取软件包镜像。

## 1.使用rsync工具
执行命令拉取源中的内容

可根据路径目录的不同来拉取需要的包

```bash
#https://mirrors.tuna.tsinghua.edu.cn/
#清华源地址

#下载rsync工具
yum -y install rsync

rsync -zvaP  rsync://mirrors.tuna.tsinghua.edu.cn/centos/7.9.2009/cloud/x86_64/openstack-train/repodata/ \
/home/repodata
#上方命令从清华大学源拉取T版openstack的repo效验包到本地的/home/repodata/目录
```

rsync -zvaP 是一个用于同步文件的命令。

其中 ：

-z 表示压缩传输

-v 表示输出详细信息

-a 表示归档模式，保留文件属性和权限

-P 表示同时保留文件传输的进度信息
