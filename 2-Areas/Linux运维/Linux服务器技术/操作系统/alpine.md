# alpine

Alpine Linux 轻量级操作系统安装与配置。



alpine 源文件存在 /etc/apk/repositories目录下，直接修改这里面的文件地址即可

vi /etc/apk/repositories

替换源文件为

[http://mirrors.aliyun.com/alpine/v3.12/main](http://mirrors.aliyun.com/alpine/v3.12/main)

[http://mirrors.aliyun.com/alpine/v3.12/community](http://mirrors.aliyun.com/alpine/v3.12/community)

推荐使用如下的方式直接修改

```bash
#阿里镜像
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

#科大镜像
sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
```

```bash
搜索软件包：apk search <package_name>
例如：#apk search nginx

安装软件包：apk add <package_name>
例如：#apk add nginx

删除软件包：apk del <package_name>
例如：#apk del nginx

更新软件包索引：#apk update
这个命令会更新本地软件包索引，使得你可以搜索到最新的软件包信息。

显示已安装的软件包：#apk info
这个命令会列出所有已经安装的软件包。
```
