# SSH 免密登录

通过 SSH 密钥对(公钥 + 私钥)实现主机间无密码登录,常用于自动化部署、批量管理、Git 推送、rsync 同步等。

## 原理

```text
┌─────────────┐                      ┌─────────────┐
│  Client     │                      │   Server    │
│ ~/.ssh/     │   1. SSH 连接请求    │ ~/.ssh/     │
│   id_rsa    │ ───────────────────▶ │             │
│   id_rsa.pub│                      │ authorized_ │
│             │  2. 服务器用 client   │   keys      │
│             │     公钥发挑战        │             │
│             │ ◀─────────────────── │             │
│             │                      │             │
│ 3. 客户端用  │                      │             │
│    私钥解密  │                      │             │
│     挑战     │ ───────────────────▶ │             │
│             │                      │ 4. 验证通过  │
│             │                      │   允许登录   │
└─────────────┘                      └─────────────┘
```

**关键**:客户端的**公钥**先放到服务器的 `~/.ssh/authorized_keys`。

## 步骤

### 1. 在客户端生成密钥对

```bash
# 推荐用 ed25519(更短、更快、更安全)
ssh-keygen -t ed25519 -C "your_email@example.com"

# 或用 RSA 4096(兼容性最好,旧系统不支持 ed25519)
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 完全无交互(脚本场景)
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

# 默认生成两个文件:
#   ~/.ssh/id_rsa      ← 私钥(绝对不能泄露!)
#   ~/.ssh/id_rsa.pub  ← 公钥(可公开)
```

### 2. 把公钥放到服务器

```bash
# 方式一:ssh-copy-id(推荐,自动处理权限)
ssh-copy-id -i ~/.ssh/id_rsa.pub user@server

# 指定端口
ssh-copy-id -p 2222 -i ~/.ssh/id_rsa.pub user@server

# 方式二:手动追加(适用于无法用 ssh-copy-id 的环境)
cat ~/.ssh/id_rsa.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### 3. 测试

```bash
# 第一次可能要确认 host key(输 yes)
ssh user@server

# 之后应该直接登录,不再问密码
ssh user@server "hostname"
```

## 权限要求(关键!)

SSH 对权限非常严格,权限不对会**直接拒绝密钥登录**(常见坑):

```bash
# 客户端
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa          # 私钥必须 600
chmod 644 ~/.ssh/id_rsa.pub

# 服务器
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 755 ~                       # 家目录不能是 777
```

## 配置简化(~/.ssh/config)

```bash
# ~/.ssh/config
Host prod
    HostName 192.168.1.100
    User deploy
    Port 22
    IdentityFile ~/.ssh/id_rsa_prod

Host github.com
    User git
    IdentityFile ~/.ssh/id_rsa_github

# 之后可直接:
ssh prod
git clone git@github.com:user/repo.git
```

## 安全加固

服务器端 `/etc/ssh/sshd_config`:

```text
# 禁用密码登录(只允许密钥)
PasswordAuthentication no

# 禁用 root 直接登录(推荐用普通用户 + sudo)
PermitRootLogin no
# 或 PermitRootLogin prohibit-password(允许密钥但不允许密码)

# 限制只允许某些用户
AllowUsers deploy alice

# 重启 sshd 生效
systemctl restart sshd
```

## 常见问题

| 问题 | 排查 |
| --- | --- |
| 还是要密码 | 服务器 `~/.ssh` 权限错(必须 700),`authorized_keys` 必须 600 |
| `Permission denied (publickey)` | 公钥没加对,或 `~/.ssh` 不存在,或 SELinux 限制 |
| `Agent admitted failure to sign` | ssh-agent 缓存问题,`ssh-add ~/.ssh/id_rsa` 重新加 |
| 多个密钥不知道用哪个 | 用 `~/.ssh/config` 显式指定,或 `ssh -i ~/.ssh/key user@host` |
| 想让 GitHub 用密钥 | 把 `id_rsa.pub` 内容加到 GitHub → Settings → SSH keys |

## 相关笔记
- [[SSH配置详解]] — sshd_config 详解
- [[ssh免密]] — 本文
- [[Linux-virt-Bridge]]
- [[MOC-Linux运维]]
