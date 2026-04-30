# zerotier

zerotier相关技术笔记。

[https://my.zerotier.com/](https://my.zerotier.com/network)

[https://zhuanlan.zhihu.com/p/507274316](https://zhuanlan.zhihu.com/p/507274316)

```bash
zerotier-one -d

#如果是私有的网络，需要手动去官方的控制台添加zerotier-cli info 输出的id号
zerotier-cli join <network-id>：加入指定的 Zerotier 网络。
zerotier-cli status：显示当前节点的 Zerotier 运行状态。
zerotier-cli listnetworks：列出当前已加入的 Zerotier 网络。
zerotier-cli leave <network-id>：离开指定的 Zerotier 网络。

zerotier-cli info <network-id>：获取指定 Zerotier 网络的详细信息。
zerotier-cli listpeers：列出当前节点的所有对等节点（peers）信息。
zerotier-cli set <parameter> <value>：设置指定的参数值，例如 zerotier-cli set allowGlobal=true 可以允许全局流量通过 Zerotier 网络。
zerotier-cli get <parameter>：获取指定参数的值，例如 zerotier-cli get allowGlobal 可以查询全局流量是否被允许。
zerotier-cli orbit <node-id> <moon-ip>：将一个节点设置为 Moon 节点，用于自组织网络的管理。
```

```yaml
version: "3"
services:
  zt:
    image: zerotier/zerotier:1.12.2
    container_name: zt
    restart: always
    cpus: 0.5
    mem_limit: 100m
    network_mode: "host"
    devices:
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
      - SYS_ADMIN
    command: ["12ac4a1e71249b48"]  #<network-id>官网获取的id号
    volumes:
      - /var/lib/zerotier-one:/var/lib/zerotier-one
```
