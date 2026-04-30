# openclash

openclash相关技术笔记。



docker启动openwrt:[https://blog.simpdog.me/posts/using-docker-to-deploy-openwrt-as-a-home-router/](https://blog.simpdog.me/posts/using-docker-to-deploy-openwrt-as-a-home-router/)

```bash
/etc/openclash/core/
#clash 内核文件存放路径

root@iStoreOS:/etc/openclash/core# ls
clash      clash_tun

```

```bash
opkg install coreutils-nohup bash iptables dnsmasq-full curl  \
ca-certificates ipset ip-full iptables-mod-tproxy iptables-mod-extra \
libcap libcap-bin ruby ruby-yaml kmod-tun kmod-inet-diag unzip \
luci-compat luci luci-base
```
