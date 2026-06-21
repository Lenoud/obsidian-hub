# Miraibot 部署 QQ 机器人

Mirai 是一个纯 Kotlin/Java 实现的 QQ 机器人框架,通过逆向 QQ Android 客户端协议工作。Miraibot 提供了一套完整的部署、管理、插件体系,可用于群管理、消息回复、定时任务等。

> ⚠️ **重要风险提示**:使用第三方 QQ 机器人框架**违反腾讯 QQ 用户协议**,存在账号被封禁风险。建议使用小号或专用测试号,不要用主号。腾讯在 2024 年起对非官方协议的打击力度加大,可考虑转向官方的 **QQ 开放平台机器人**(基于 WebSocket,需企业/工作室认证)。

## 架构

```text
┌──────────────────────────────────────┐
│  mirai-api-http(暴露 HTTP/WebSocket)│
└────────────┬─────────────────────────┘
             │
┌────────────▼─────────────────┐
│  Mirai Console(核心运行时)  │
│   - 协议层:mirai-core       │
│   - 插件管理:Console         │
└────────────┬─────────────────┘
             │
┌────────────▼─────────┐
│  QQ Android 协议      │
│  (登录、收发消息)      │
└──────────────────────┘
```

## 部署方式

### 方式一:Mirai Console Loader(MCL,官方推荐)

```bash
# 1. 安装 Java 11+
apt install -y openjdk-11-jre

# 2. 下载 MCL(Mirai Console Loader)
wget https://github.com/iTXTech/mirai-repo/raw/master/mcl-installer/mcl-installer-2.1.6.sh
chmod +x mcl-installer-2.1.6.sh
./mcl-installer-2.1.6.sh

# 3. 启动(会自动生成 config 文件夹)
cd mcl
./mcl

# 首次启动会进入登录交互:
# LoginAccount: 10012345678
# LoginPassword: ******
```

### 方式二:Docker

```bash
docker run -d \
  --name mirai \
  --restart=always \
  -p 8080:8080 \
  -v /opt/mirai/config:/app/config \
  -v /opt/mirai/data:/app/data \
  -v /opt/mirai/bots:/app/bots \
  -e LANG=zh_CN.UTF-8 \
  officialalexander/mirai
```

## 安装插件

插件放至 `plugins/` 目录,MCL 会自动加载:

```bash
# 常用插件:
# mirai-api-http    暴露 HTTP/WebSocket,供外部调用
# chat-command      让机器人响应聊天命令
# mirai-bot-spring  Spring Boot 集成

# 通过 MCL 安装
./mcl --update-package net.mamoe:mirai-api-http --type plugin
./mcl --update-package io.github.kemingyou:chat-command --type plugin
./mcl
```

> 插件市场:<https://mirai.mamoe.net/category/11/插件发布>

## 配置 mirai-api-http

`config/net.mamoe.mirai-api-http/setting.yml`:

```yaml
adapters:
  - http
  - ws
debug: false
host: 0.0.0.0
port: 8080
authKey: your-secret-key-12345
cacheSize: 4096
```

外部通过 HTTP 调用:

```bash
# 1. 鉴权,获取 session
curl -X POST http://localhost:8080/auth \
  -H "Content-Type: application/json" \
  -d '{"authKey": "your-secret-key-12345", "qq": 10012345678}'
# {"code":0,"session":"abc123"}

# 2. 绑定机器人
curl -X POST http://localhost:8080/verify \
  -d '{"sessionKey":"abc123","qq":10012345678}'

# 3. 发消息
curl -X POST http://localhost:8080/sendFriendMessage \
  -d '{"sessionKey":"abc123","target":10012345679,"messageChain":[{"type":"Plain","text":"hello"}]}'
```

## 常见问题

| 问题 | 解决 |
| --- | --- |
| 登录提示"当前账号可能存在被盗风险" | 腾讯风控,用同一设备登录一次手机 QQ,再尝试 |
| 协议版本不支持 | 升级 `mirai-core`,或用带最新协议的 MCL |
| 消息发不出 | 检查 mirai-api-http 是否启动、authKey 是否正确 |
| 滑块验证码 | 需要在手机 QQ 上完成验证 |

## 替代方案(推荐)

| 框架 | 协议 | 备注 |
| --- | --- | --- |
| **go-cqhttp** | 逆向 Android QQ | Go 实现,稳定,文档完善(已暂停维护,继续分支 **Lagrange.Core**) |
| **Lagrange.Core** | NTQQ 协议 | C#,较新 |
| **NoneBot2** | 框架 | 配合 go-cqhttp/Lagrange 用,Python 写插件 |
| **QQ 开放平台官方机器人** | 官方 WebSocket | 需认证,无封号风险 |

## 相关链接

- Mirai 官网:<https://github.com/mamoe/mirai>
- 插件市场:<https://mirai.mamoe.net/category/11/插件发布>
- 文档:<https://github.com/mamoe/mirai/blob/dev/docs/UserManual.md>

## 相关笔记
- [[MOC-服务部署]]
