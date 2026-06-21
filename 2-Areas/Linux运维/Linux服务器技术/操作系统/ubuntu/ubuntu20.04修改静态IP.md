# ubuntu20.04修改静态IP

Ubuntu18后的网卡配置被netplan替代了，命令也变更了

> 注意：`gateway4` 在 Ubuntu 22.04 / netplan 0.100+ 中已被废弃，新版需改用 `default routes` 写法，见下方说明。

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

> Ubuntu 22.04+ 推荐写法（使用 routes 替代 gateway4）：
>
> ```yaml
> network:
>   ethernets:
>     ens33:
>       dhcp4: false
>       addresses: [192.168.100.135/24]
>       routes:
>         - to: default
>           via: 192.168.100.2
>       nameservers:
>         addresses: [114.114.114.114,8.8.8.8]
> ```
