# openwrt

openwrt相关技术笔记。



下载地址

[https://downloads.openwrt.org/releases/](https://downloads.openwrt.org/releases/)

下载img镜像

[https://downloads.openwrt.org/releases/22.03.5/targets/x86/64/](https://downloads.openwrt.org/releases/22.03.5/targets/x86/64/)

下载img转vmdk工具

StarWind V2V Image Converter

中文包下载 搜索 luci-i18n-base-zh-cn_git-23.154.41063-b9a0188_all.ipk

[https://downloads.openwrt.org/releases/22.03.5/packages/x86_64/luci/](https://downloads.openwrt.org/releases/22.03.5/packages/x86_64/luci/)

# 换源
```bash
cat > /etc/opkg/distfeeds.conf << EOF
src/gz openwrt_core https://mirrors.aliyun.com/openwrt/releases/22.03.5/targets/x86/64/packages
src/gz openwrt_base https://mirrors.aliyun.com/openwrt/releases/22.03.5/packages/x86_64/base
src/gz openwrt_luci https://mirrors.aliyun.com/openwrt/releases/22.03.5/packages/x86_64/luci
src/gz openwrt_packages https://mirrors.aliyun.com/openwrt/releases/22.03.5/packages/x86_64/packages
src/gz openwrt_routing https://mirrors.aliyun.com/openwrt/releases/22.03.5/packages/x86_64/routing
src/gz openwrt_telephony https://mirrors.aliyun.com/openwrt/releases/22.03.5/packages/x86_64/telephony
EOF
```

完成后配置ip,到web管理页面中的system --> software导入包安装，完成后刷新页面

# 安装完毕后编辑配置 /etc/config/network
```yaml

config interface 'loopback'
	option device 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fd6a:2874:9282::/48'

config device
	option name 'br-lan'
	option type 'bridge'
	list ports 'eth0'
	option ipv6 '0'

#定义两个wan IP
#这个wan ip就变为其它虚拟机的gateway
config interface 'wan'
	option device 'br-lan'
	option proto 'static'
	option ipaddr '10.1.25.60'
	option netmask '255.255.224.0'
	option broadcast '10.1.31.255'
	list dns '10.100.10.100'  #校园网dns
	list dns '114.114.114.114'
	option gateway '10.1.25.65'  #第二个wan口IP

#这个wan IP连接校园网网关
config interface 'wan_1'
	option proto 'static'
	option device 'br-lan'
	option ipaddr '10.1.25.65'
	option netmask '255.255.224.0'
	option gateway '10.1.0.1'  #校园网网关

#定义一个内网 内网机器填写这个ip
config interface 'lan'
	option proto 'static'
	option ipaddr '192.168.100.1'
	option netmask '255.255.255.0'
	option device 'br-lan'

```

命令应用配置

ifup eth0

# 刷新分区表
```bash
mount -o remount,rw /dev/sda1 /
opkg update
opkg install gdisk fdisk lsblk resize2fs  parted
gdisk /dev/sda
```

# 使用VMware启动vmdk并且设置硬盘大小
将两个包复制到openwrt目录下

![[1696255636157-3c276082-c90c-4b56-b142-7b18aded8235.png]]

![[1696255706146-e7635f96-c95f-41aa-9dfd-69ce4f6703e7.png]]

使用现有磁盘，选择第一个

![[1696255730978-7e7efba0-e1b5-4f4c-b668-e0480509a773.png]]

编辑配置，扩展磁盘即可

![[1696255799155-d7a3166e-696c-40bc-9466-a6fd2169f7a7.png]]
