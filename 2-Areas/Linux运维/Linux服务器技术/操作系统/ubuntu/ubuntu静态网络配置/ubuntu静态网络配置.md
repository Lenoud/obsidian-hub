# ubuntu静态网络配置

Ubuntu 21.04+ 静态网络配置（netplan）。

21.04版本后配置文件发生了一点小改变

```yaml
network:
  ethernets:
    ens192:
      dhcp4: false
      addresses: [10.1.25.61/19]
      routes:
        - to: 0.0.0.0/0
          via: 10.1.25.65
      nameservers:
        addresses: [10.100.10.100,114.114.114.114]
```
