# kvm虚拟化部署

安装工具

```shell

#安装kvm和依赖包
yum -y install mesa-libGLES-devel.x86_64 mesa-dri-drivers
yum -y install qemu-kvm libvirt virt-manager
#解决客户端可能无法开启图形化界面
yum -y install xorg-x11-font-utils xorg-x11-xauth  xorg-x11-xinit  dbus-x11
echo "export LANG=en_US"  >> /etc/profile  #解决图形化管理页面中文乱码
#安装命令行工具
yum -y install libguestfs-tools
yum -y install virt-install
#推荐使用MobaXterm工具连接服务器，原生支持x11转发，可以直接使用图形化管理
```

解决权限错误

vi /etc/libvirt/qemu.conf

user = "root"

group = "root"

dynamic_ownership = 0

vnc_listen = "0.0.0.0"
