# EVE-NG(网络模拟器)

EVE-NG(Emulated Virtual Environment Next Generation)是基于 Ubuntu 定制的网络模拟平台,支持 Cisco / Juniper / 华为 / Arista / F5 等各大厂商镜像,用于网络拓扑实验、认证考试(CCIE / HCIE)、培训。是 GNS3 / Packet Tracer 的强大替代品。

## 版本区别

| 版本 | 定位 | 限制 |
| --- | --- | --- |
| **Learning Edition**(免费) | 个人学习 | 最多 110 个节点,IOL/QEMU 受限 |
| **Professional** | 商业 | 无节点限制,多用户 |
| **Learning Edition Pro** | 免费(注册版) | 解锁更多功能 |

## 默认账号

```text
# Web 界面登录
用户名: admin
密码:  eve

# SSH 登录(系统层)
用户名: root
密码:  eve
```

## 系统架构

EVE-NG 底层是定制版 Ubuntu:

```text
# 根文件系统
/
├── opt/unetlab/           # EVE 主程序
│   ├── addons/            # 附加组件
│   └── html/              # Web 界面
├── opt/qemu/              # QEMU 二进制(不同版本)
└── tmp/{upload,swfs}/     # 临时上传

# 镜像目录
/opt/unetlab/addons/
├── iol/bin/               # Cisco IOL 镜像(Linux 上的 IOS)
├── qemu/                  # QEMU 镜像(ASA/IOSv/Router/Nexus 等)
│   ├── linux-xxx/         # Linux 微软主机
│   └── ...
└── dynamips/              # Dynamips IOS 镜像(老式)

# 拓扑数据
/opt/unetlab/labs/         # .unl 拓扑文件(JSON 格式)
/opt/unetlab/tmp/<user_id>/<lab_md5>/   # 运行时数据
```

## 上传镜像

```bash
# 上传 Cisco IOL 镜像(SSH)
scp L3-*.bin root@eve-ng:/opt/unetlab/addons/iol/bin/

# 上传 QEMU 镜像(目录形式)
scp -r viosl2-*.qcow2 root@eve-ng:/opt/unetlab/addons/qemu/

# 修正权限
chown -R root:root /opt/unetlab/addons/
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```

## 常用运维命令

```bash
# 服务状态
systemctl status apache2
systemctl status mysql

# 重启 EVE 服务
systemctl restart apache2
systemctl restart mysql

# 清理挂起的实验
for f in /opt/unetlab/tmp/*/*/*; do
  [ -d "$f" ] && rm -rf "$f"
done

# 查看 CPU/内存负载(实验跑起来后)
htop
iostat -x 1

# 查看运行中的 QEMU 实例
ps aux | grep qemu
```

## 性能优化建议

| 维度 | 建议 |
| --- | --- |
| CPU | 多核,支持 VT-x / AMD-V(必须) |
| 内存 | 每个节点至少 512MB-IOL,1-4GB-QEMU(IOSvL2、Nexus) |
| 存储 | SSD 必备,QEMU 节点对 IO 敏感 |
| 网络 | 用桥接到物理网卡,不要全 NAT |
| Hypervisor | 如果 EVE 本身跑在 ESXi/PVE 上,开 CPU 性能透传(host-passthrough) |

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| 节点启动后红色(失败) | 镜像权限/路径错误 | `unl_wrapper -a fixpermissions` |
| CPU 100% 卡顿 | 节点 idlepc 未设置(Dynamips) | 选中节点右键 set idlepc |
| Web 登录后白屏 | 浏览器缓存 / PHP 错误 | 清缓存,查 `/opt/unetlab/data/logs/php.log` |
| 中文路径乱码 | 实验文件名含中文 | 改名,或加 `LANG=zh_CN.UTF-8` |

## 相关笔记
- [[Linux基础命令]] — EVE 底层就是 Linux
- [[KVM虚拟化]] — EVE 节点用 QEMU/KVM
- [[MOC-Linux运维]]
