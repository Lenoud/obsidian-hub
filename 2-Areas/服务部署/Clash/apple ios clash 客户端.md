# Apple iOS Clash 客户端

iOS 平台的代理客户端选型。由于 iOS 系统限制,所有代理应用都必须基于 iOS 提供的 NetworkExtension 框架开发,功能受系统 VPN 配额限制(同时只能开一个)。

## 客户端对比

| 应用 | 价格 | 支持协议 | 备注 |
| --- | --- | --- | --- |
| **Spectre VPN** | 免费 | Shadowsocks、Trojan | 轻量,协议支持有限 |
| **Shadowrocket(小火箭)** | $2.99(美区) | SS、SSR、Trojan、VMess、VLESS、Hysteria、WireGuard | 推荐,生态最全 |
| **Quantumult X** | $9.99(美区) | SS、Trojan、VMess、HTTP | 灵活的分流规则、MitM |
| **Surge 5** | $49.99 起 | SS、VMess、Trojan、WireGuard | 专业,调试强大,贵 |
| **Loon** | $7.99 | SS、VMess、Trojan | Quantumult X 替代 |
| **Stash** | $3.99 | Clash 格式 | 原生支持 Clash 配置 |

## Spectre VPN(免费推荐)

**Spectre** 是 iOS 平台难得的**免费**代理工具,支持 Shadowsocks 和 Trojan 协议。轻量、高效,但不支持 V2Ray(VMess)、Xray(VLESS)等其他协议。

如果机场服务商提供 SS 或 Trojan 协议,直接用免费的 Spectre 即可。

**安装**:App Store 搜索 "Spectre VPN"。

## Shadowrocket(付费推荐)

**小火箭**是 iOS 平台功能最全的代理客户端之一,协议支持最广泛,适合:

- 用 V2Ray/Xray 协议的机场
- 需要复杂的分流规则(国内直连 / 国外代理)
- 需要订阅管理(导入订阅链接后,自动同步节点)

**安装**:App Store 美区搜索 "Shadowrocket",价格 $2.99。

## 选型建议

| 你的需求 | 推荐 |
| --- | --- |
| 只用 SS/Trojan,想免费 | **Spectre VPN** |
| 想要全协议支持,愿意付费 | **Shadowrocket**(性价比最高) |
| 想用 Clash 格式的订阅(从 Clash for Windows / Stash 切过来) | **Stash** |
| 专业调试、复杂规则 | **Surge 5** |

## 相关笔记
- [[clash部署]] — Clash 各平台
- [[openclash]] — 路由器上的 OpenClash
- [[proxies解析]] — Clash 配置 proxies 段解析
- [[MOC-服务部署]]

