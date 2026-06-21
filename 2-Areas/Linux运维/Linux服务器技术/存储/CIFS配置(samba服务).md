# 服务端配置

CIFS/Samba 文件共享服务的配置。
```ini
# 安装Samba服务
yum install -y samba

# 创建共享⽬录并放开权限
mkdir -p /opt/share
chmod 777 /opt/share

# 编辑Samba的配置⽂件,添加如下类容
cat  >> /etc/samba/smb.conf <<EOF
[share]
comment=share
path=/opt/share
#声明共享的目录
browseable=yes
public=yes
writable= yes
EOF
# 创建Samba⽤户（该⽤户必须是系统存在的⽤户）
smbpasswd -a root
# 重启smb服务
systemctl restart smb
systemctl enable smb
#//查看139端口是否开启
netstat -ntpl |grep 139
```

# 客户端端配置
```bash
# 安装Samba服务
yum install -y samba
yum install -y samba-client
#连接CIFS服务端进⾏测试（出现share）
smbclient -NL \\192.168.200.131
# 创建空⽬录
mkdir -p /mnt/cifs
# 客户端挂载CIFS共享⽬录。⽤户名和密码是服务端创建的⽤户名、密码，IP改成服务端的IP。
mount -t cifs -o username=root,password=000000 //192.168.200.131/share   /mnt/cifs
# 查看挂载点
df -h
```

# win10挂载cifs
一、页面挂载与解挂

1、挂载

右键“我的电脑”，点击“映射网络驱动器”

驱动器可以选择默认，直接在文件夹中输入\\192.168.10.11\cifs （\\ip\共享名称）

输入用户名+密码   用户名密码需要在存储服务器系统里创建，

挂载成功后，查看挂载磁盘属性，检查挂载磁盘是否是NTFS，如果是可直接写入；

如果不是，须将磁盘格式化成NTFS。

2、解挂：在“我的电脑”里，选中挂载的盘符->”右键“，点击”断开连接“。

二、使用命令提示符进行挂载/解挂

1、挂载CIFS共享至Z盘

net use Z: \\192.168.11.11\cifs_qiang 1qaz!QAZ /USER:user /PERSISTENT:YES

2、解挂所有：

net use * /del /y

## smb_v1不安全提解决
[https://blog.csdn.net/l_liangkk/article/details/80646266](https://blog.csdn.net/l_liangkk/article/details/80646266)
