# 命令行连接WIFI

在命令行连接WIFI对于特殊情况下是很有必须要的。

```bash
查看是不是识别出了无线网卡：

$ sudo iwconifg
启动无线网卡（假如是wlan0）

$ sudo ifconfig wlan0 up
以前网络的配置是 /etc/network/interfaces

现在是 /etc/netplan/XXX.yaml
```

在 yaml 文件中 network: 字段下加配置如下：

```yaml
renderer: NetworkManager
wifis:
    wlan0:
        dhcp4: true
        access-points:
            "WIFINAME":
                password: "PASSWORD"
```

然后安装软件和应用：

```bash
$ sudo apt install wpasupplicant network-manager
$ sudo netplan generate
$ sudo netplan apply
```
