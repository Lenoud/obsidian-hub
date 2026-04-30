# ubuntu20.04修改静态IP

Ubuntu18后的网卡配置被netplan替代了，命令也变更了

修改配置文件

```yaml
# Let NetworkManager manage all devices on this system
network:
  ethernets:
    ens33:
      dhcp4: false
      addresses: [192.168.100.135/24]
      gateway4: 192.168.100.2
      nameservers:
        addresses: [114.114.114.114,8.8.8.8]
```

#生效配置

netplan --debug apply
