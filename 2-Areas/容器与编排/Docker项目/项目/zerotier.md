# ZeroTier 虚拟组网

开源的虚拟局域网工具，通过 P2P 技术将不同物理网络的设备连接到同一个虚拟网络中。

## 部署

```yaml
version: "3"
services:
  zt:
    image: zerotier/zerotier:latest
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
    command: ["bb720a5aaefbd742"]  #<network-id>官网获取的id号
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/lib/zerotier-one:/var/lib/zerotier-one
```

## 说明

- 使用 `network_mode: host`，直接使用主机网络
- 资源限制：0.5 核 CPU、100M 内存
- `command` 中的 Network ID 需替换为 ZeroTier 官网创建的网络 ID
- `devices: /dev/net/tun`：挂载 TUN 设备用于虚拟网络
- `cap_add`：添加 `NET_ADMIN` 和 `SYS_ADMIN` 权限
- 数据持久化：`/var/lib/zerotier-one` 映射到宿主机同名目录
