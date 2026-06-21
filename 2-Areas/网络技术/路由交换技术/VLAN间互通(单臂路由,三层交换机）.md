# VLAN 间互通(单臂路由 / 三层交换机)

不同 VLAN 之间默认**相互隔离**(二层广播域不同),要让 VLAN A 与 VLAN B 通信,需要在三层(网络层)做路由转发。常见的两种方案:**单臂路由** 和 **三层交换机**。

## 方案对比

| 方案 | 实现 | 优点 | 缺点 | 适用场景 |
| --- | --- | --- | --- | --- |
| **单臂路由** | 路由器一个物理口 + 多个子接口 | 不需要专门交换机,成本低 | 单点(单链路)瓶颈,路由器转发性能有限 | 小型网络、教学实验 |
| **三层交换机** | 交换机内置路由模块(SVI 接口) | 线速转发,无单点瓶颈 | 设备较贵 | 中大型企业网、数据中心 |

## 方案一:单臂路由(Router-on-a-Stick)

### 拓扑

```text
       VLAN10: 192.168.10.0/24            VLAN20: 192.168.20.0/24
PC1 ────┐                                     ┌──── PC2
        │ (access)                            │ (access)
        ▼                                     ▼
       G0/1                                 G0/2
        ┌──────────── Switch ─────────────┐
        │   VLAN 10     VLAN 20  (trunk)  │
        └─────────────────┬───────────────┘
                          │ G0/24 (trunk)
                          ▼
        ┌─────────── Router ────────────┐
        │  G0/0.10 → 192.168.10.1/24    │ ← VLAN10 网关
        │  G0/0.20 → 192.168.20.1/24    │ ← VLAN20 网关
        └───────────────────────────────┘
```

### 交换机配置

```text
! 创建 VLAN
vlan 10
 name VLAN10
vlan 20
 name VLAN20

! 接入端口(access)
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10

interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 20

! 上行到路由器(trunk)
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20
```

### 路由器配置

```text
! 物理接口不起 IP,只启用
interface GigabitEthernet0/0
 no shutdown

! 子接口 0.10(VLAN 10 网关)
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

! 子接口 0.20(VLAN 20 网关)
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

## 方案二:三层交换机(Layer 3 Switch)

三层交换机内置路由模块,直接在交换机内部完成 VLAN 间路由,**性能比单臂路由高得多**(硬件转发)。

### 配置

```text
! 1. 创建 VLAN
vlan 10
 name VLAN10
vlan 20
 name VLAN20

! 2. 开启全局路由功能(关键!)
ip routing

! 3. 配置 SVI(Switch Virtual Interface,VLAN 接口)
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

! 4. 接入端口(access)
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10

interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 20
```

## 验证

```text
# 在交换机上查看路由表(三层交换机)
show ip route
# C    192.168.10.0/24 is directly connected, Vlan10
# C    192.168.20.0/24 is directly connected, Vlan20

# 在 PC1(192.168.10.100)ping PC2(192.168.20.100)
ping 192.168.20.100
```

## 常见问题

| 问题 | 原因 |
| --- | --- |
| 同 VLAN 通,跨 VLAN 不通 | 单臂路由:trunk allowed vlan 没允许目标 VLAN;三层交换机:忘加 `ip routing` |
| 单臂路由性能差 | 路由器 CPU 处理,建议升级三层交换机 |
| 路由器子接口封装错 | `encapsulation dot1Q <vlan-id>` 必须和交换机 trunk 的 VLAN 对应 |

## 相关笔记
- [[交叉线与直通线的区别]] — 物理线缆
- [[局域网与广域网]]
- [[NAT(网络地址转换)]]
- [[MOC-网络技术]]
