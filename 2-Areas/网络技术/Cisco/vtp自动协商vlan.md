# VTP 自动协商 VLAN

VTP(VLAN Trunking Protocol)是 Cisco 私有协议,用于在交换机之间自动同步 VLAN 信息,避免在网络中每台交换机上手动创建 VLAN。一台 VTP Server 上的 VLAN 配置变更会自动传播到同域内的其他交换机。

## VTP 模式

| 模式 | 能创建/修改/删除 VLAN | 能转发 VTP 报文 | 能保存到 vlan.dat | 说明 |
| --- | --- | --- | --- | --- |
| **Server**(服务器) | ✅ | ✅ | ✅ | 默认模式,可管理 VLAN |
| **Client**(客户端) | ❌ | ✅ | ✅ | 只接收同步,不能创建 |
| **Transparent**(透明) | ✅(仅本地) | ✅(直通转发) | ❌ | 不参与同步,独立运行 |

## 配置步骤

### Server 端(核心交换机)

```text
# 进入全局配置模式
Switch# configure terminal

# 设置 VTP 域名(域名必须与 Client 一致)
Switch(config)# vtp domain SKYRIS

# 设置模式为 server
Switch(config)# vtp mode server

# (可选)设置 VTP 密码,防止非法交换机加入
Switch(config)# vtp password Cisco@123

# 创建 VLAN
Switch(config)# vlan 10
Switch(config-vlan)# name办公
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name业务

# 配置 trunk 链路(否则 VTP 报文不会传)
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk
```

### Client 端(接入交换机)

```text
Switch# configure terminal

# 同样的域名
Switch(config)# vtp domain SKYRIS

# 同样的密码
Switch(config)# vtp password Cisco@123

# 设置模式为 client
Switch(config)# vtp mode client

# 配置 trunk
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport mode trunk

# 验证:此时在 Client 上 show vlan 应该能看到从 Server 同步过来的 VLAN
Switch# show vlan brief
```

## 验证命令

```text
# 查看 VTP 配置(版本、域名、模式、修订号、VLAN 数量)
Switch# show vtp status

# 查看 VTP 密码
Switch# show vtp password

# 查看 VLAN(同步过来的 VLAN 类型会显示为 active)
Switch# show vlan brief
```

## 重要注意事项

- **配置修订号(Configuration Revision)是双刃剑**:修订号高的交换机会覆盖修订号低的。把一台旧的 Server 接入网络时,如果它的修订号比现网高,会把现网 VLAN 全部覆盖,**强烈推荐接入前用 `vtp mode transparent` 再改回 server/client 以清零修订号**。
- VTP v1/v2 只能同步 VLAN 的存在,不能同步 VLAN 内具体端口分配;**VTP v3** 才支持端口级别同步,且支持私有 VLAN。
- VTP 同步信息保存在 `flash:vlan.dat`,不在 startup-config,erase startup-config 不会清除 VLAN 数据库。
- 配置 trunk 链路(DTP 协商或硬编码 `switchport mode trunk`)是 VTP 工作的前提。

## 相关笔记
- [[MOC-网络技术]]
- [[ACL（访问控制列表）]] — Cisco 流量控制
- [[NAT配置]] — Cisco 路由器 NAT
