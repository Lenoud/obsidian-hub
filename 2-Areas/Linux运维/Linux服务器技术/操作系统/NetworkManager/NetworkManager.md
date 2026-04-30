# NetworkManager

NetworkManager 网络管理工具使用指南。

```bash
# 查看设备信息
[root@localhost ~]# nmcli device
DEVICE  TYPE      STATE                   CONNECTION
eno1    ethernet  connected               eno1
lo      loopback  connected (externally)  lo
eno2    ethernet  unavailable             --

# 配置 IPv4 地址
[root@localhost ~]# nmcli connection modify eno1 ipv4.addresses 192.168.10.10/24

# 配置 IPv4网关
[root@localhost ~]# nmcli connection modify eno1 ipv4.gateway 192.168.10.254

# 配置 IPv4 DNS，多个 DNS IP 之间使用双引号 + 空格
[root@localhost ~]# nmcli connection modify eno1 ipv4.dns "114.114.114.114 223.6.6.6"

# 设置 DNS 基础搜索，多个域名之间使用双引号 + 空格
[root@localhost ~]# nmcli connection modify eno1 ipv4.dns-search "rockylinux.cn rockylinux.org"

# 重新加载网络配置
[root@localhost ~]# nmcli connection down eno1; nmcli connection up eno1
```

```yaml
#列出所有连接：
nmcli connection show
#启用/禁用网络连接：
nmcli networking on
nmcli networking off
#查看网络设备状态：
nmcli device status
#连接到一个wifi网络：
nmcli device wifi connect <SSID> password <password>
#断开连接：
nmcli connection down <connection-name>
#重新启动连接：
nmcli connection up <connection-name>
#添加一个新连接：
nmcli connection add type <connection-type> ifname <interface-name> con-name <connection-name>
#修改连接属性：
nmcli connection modify <connection-name> <property> <value>
#删除一个连接：
nmcli connection delete <connection-name>
#查看详细连接信息：
nmcli connection show <connection-name>
#查看网络连接的IP地址：
nmcli connection show <connection-name> | grep IP4
#显示网络设备的详细信息：
nmcli device show <device-name>
#查看连接的DNS服务器：
nmcli connection show <connection-name> | grep DNS
#显示网络设备的Wi-Fi连接列表：
nmcli device wifi list
#列出所有可用的连接类型：
nmcli connection show available
#刷新网络连接列表：
nmcli connection reload
#查看网络管理器状态：
nmcli general status
```
