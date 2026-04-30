# gedit文本编辑工具

gedit 是 GNOME 桌面环境的图形化文本编辑工具。

```shell
#安装gedit工具
yum -y install gedit
#安装x11服务可在支持x11转发的cmd客户端使用图形化界面
sudo yum groupinstall "X Window System"
sudo yum install xorg-x11-server-Xorg
yum -y install xorg-x11-font-utils xorg-x11-xauth  xorg-x11-xinit  dbus-x11
#解决gui界面文字乱码
echo "export LANG=en_US"  >> /etc/profile
source /etc/profile

#X11服务器需要一个配置文件才能正确运行。默认情况下，CentOS 7
#将/etc/X11/xorg.conf文件作为配置文件。
#如果该文件不存在，则可以使用以下命令自动生成配置文件：
#这将生成一个新的配置文件 /root/xorg.conf.new
sudo Xorg :1 -configure

#启动
sudo Xorg &

#要验证X11服务器是否正在运行，请使用以下命令：
sudo netstat -antup | grep Xorg
#如果看到输出结果中有一个 Xorg 进程的存在，则表示X11服务器正在运行。

#然后可以在其他终端使用ssh -X username@remote_server_ip 连接
#连接时需要添加 -X 或 -Y 参数
ssh -Y root@192.168.100.40
```

```shell
#Ubuntu 20.04.4 安装方式
# 检查可安装程序
sudo apt list | grep xrdp
xorgxrdp/focal,now 1:0.2.12-1 amd64
xrdp/focal,now 0.9.12-1 amd64

# 安装
sudo apt install xrdp

# 当前版本为 0.9.12
xrdp -v
xrdp 0.9.12
# 启动
sudo systemctl start xrdp

# 设置开机自启动
sudo systemctl enable xrdp

# 检查监听端口
sudo ss -nlpt | grep 3389
LISTEN    0         2        *:3389     *:*        users:(("xrdp",pid=861,fd=11))
```
