# ImmortalWrt

ImmortalWrt 是 OpenWrt 的一个分支，专注于提供更好的开箱即用体验和更多软件包支持。

## 下载地址

官方固件下载：[https://downloads.immortalwrt.org/releases/](https://downloads.immortalwrt.org/releases/)

## 说明

| 特性 | 说明 |
| :--- | :--- |
| 基于 | OpenWrt |
| 包管理 | opkg，扩展源软件包更丰富 |
| 默认支持 | Flow Offload、硬件 NAT 加速 |
| 适用场景 | 软路由、旁路由、旁路网关 |

## 常用配置

```bash
# 更新软件包列表
opkg update

# 安装 LuCI 界面（如未预装）
opkg install luci

# 查看网络接口配置
cat /etc/config/network

# 重启网络服务
/etc/init.d/network restart
```
