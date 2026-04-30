# rpm使用

RPM 包管理器的常用操作命令。

```bash
rpm -qa (列出所有安装了的包)
rpm -e package (删除某个包)
rpm -qi package (查询某个包)
rpm -ql package (查询某个包所有的安装文件)
rpm -Uvh telnet-0.17-65.el7_8.x86_64.rpm 安装
```

```bash
rpm -qf /usr/bin/vim (检查文件所属的软件包)
vim-enhanced-7.4.629-8.el7_9.x86_64
```
