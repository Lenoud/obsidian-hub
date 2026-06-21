# Windows Hosts 文件

Windows 的 hosts 文件用于**本地静态 DNS 解析**,优先级高于 DNS 服务器。常用于本地测试、屏蔽广告/追踪、强制域名指向特定 IP。

## 文件路径

```text
C:\Windows\System32\drivers\etc\hosts
```

## 编辑方式

**必须以管理员身份运行**(否则无法保存):

### 方式一:命令行(管理员 cmd/PowerShell)

```cmd
# cmd
notepad.exe C:\Windows\System32\drivers\etc\hosts

# PowerShell(更可靠,自动提权)
Start-Process notepad -Verb runAs -ArgumentList "C:\Windows\System32\drivers\etc\hosts"
```

### 方式二:GUI

1. 在开始菜单搜索 "记事本"(Notepad)
2. **右键 → 以管理员身份运行**
3. 文件 → 打开 → 输入路径 `C:\Windows\System32\drivers\etc\hosts`
4. 注意:文件类型选 "所有文件 (*.*)",否则看不到 hosts(它没扩展名)

## 格式

```text
# IP 地址   域名(可多个,空格分隔)    # 注释

127.0.0.1       localhost
::1             localhost

# 自定义解析
192.168.1.100   myapi.local dev.local
127.0.0.1       activate.adobe.com      # 屏蔽 Adobe 验证
0.0.0.0         ads.example.com         # 屏蔽广告
```

## 修改后立即生效

```cmd
# 清除 DNS 解析缓存(必须执行)
ipconfig /flushdns

# 验证
ping myapi.local
nslookup myapi.local
```

## 常见问题

| 问题 | 排查 |
| --- | --- |
| 保存时提示"拒绝访问" | 没用管理员权限打开记事本 |
| 修改后浏览器仍访问旧 IP | `ipconfig /flushdns` 清缓存;浏览器也有自己的 DNS 缓存(Chrome: `chrome://net-internals/#dns`) |
| hosts 不可写,文件变成只读 | 右键文件 → 属性 → 取消"只读" |
| 杀毒软件拦截修改 | hosts 是杀软重点监控文件,临时关闭或加白 |

## 相关笔记
- [[windows 定时关机]]
- [[查看端口开放情况]]
- [[windows hosts file]] — 本文
- [[MOC-Windows]]
