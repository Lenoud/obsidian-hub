# ARP 协议

ARP（Address Resolution Protocol）地址解析协议，用于将 IP 地址映射为 MAC 地址，是局域网通信的基础协议。

## 常用命令

```bash
# 查看 ARP 缓存表
arp -a

# 查看 ARP 缓存表（详细信息）
arp -n

# 添加静态 ARP 映射
arp -s 192.168.1.1 00:11:22:33:44:55

# 删除 ARP 条目
arp -d 192.168.1.1

# 使用 ip 命令管理 ARP（推荐）
ip neigh show
ip neigh add 192.168.1.1 lladdr 00:11:22:33:44:55 dev eth0
ip neigh del 192.168.1.1 dev eth0
```

## ARP 欺骗防御

```bash
# 绑定网关 MAC 地址防止 ARP 欺骗
arp -s 192.168.1.1 aa:bb:cc:dd:ee:ff

# 使用 arptables 过滤
arptables -A INPUT --source-mac ! aa:bb:cc:dd:ee:ff -j DROP
```
