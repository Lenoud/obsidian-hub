# API 调试工具

HTTP API 调试、接口管理、自动化测试工具对比。

## 主流工具对比

| 工具 | 平台 | 免费 | 强项 | 弱点 |
| --- | --- | --- | --- | --- |
| **Apifox** | Win/Mac/Linux/Web | ✅ | 国产、中文友好、API 文档+测试+Mock 一体 | 部分高级功能需付费 |
| **Postman** | Win/Mac/Linux/Web | 免费版(限) | 全球最流行,生态成熟 | 免费版有限制,云端同步收费 |
| **Insomnia** | Win/Mac/Linux | ✅(OSS 版) | 开源、轻量、GraphQL 友好 | 团队协作功能弱 |
| **HTTPie** | 命令行 | ✅ | 命令行 HTTP 客户端,语法优雅 | 无 GUI |
| **curl** | 命令行 | ✅ | 万能,所有系统自带 | 语法繁琐 |
| **Apipost** | Win/Mac/Linux/Web | 免费版 | 国产 Postman 替代 | 用户量小 |
| **Paw(RapidAPI)** | macOS | 收费 | Mac 原生,功能强 | 仅 Mac |
| **VS Code REST Client** | VS Code 插件 | ✅ | 在编辑器里写 .http 文件,版本控制友好 | 不适合大批量管理 |
| **JetBrains HTTP Client** | IDEA/PyCharm 内置 | ✅ | 与 IDE 集成,.http 文件 | 只在 JetBrains 系 IDE |

## 推荐选择

| 场景 | 推荐 |
| --- | --- |
| 个人/小团队,中文环境 | **Apifox** |
| 跨团队协作、海外项目 | **Postman** |
| 偏命令行 | **HTTPie** 或 curl |
| 已在用 JetBrains IDE | **IDEA HTTP Client**(`.http` 文件) |
| GitHub 项目里维护接口示例 | **VS Code REST Client** |

## Apifox 简介(国产推荐)

- 官网:<https://www.apifox.com/>
- 同时是 Postman + Swagger + Mock + JMeter 的合体
- 支持团队协作、接口文档自动生成、代码生成(Java/Go/Python 等)

## 常用 curl 技巧

```bash
# GET(默认)
curl https://api.example.com/users

# POST JSON
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice","age":30}'

# 带认证
curl -H "Authorization: Bearer <token>" https://api.example.com/me

# 只看响应头
curl -I https://example.com

# 跟随重定向
curl -L https://short.url/abc

# 详细过程(含握手、TLS)
curl -v https://api.example.com

# 下载文件
curl -O https://example.com/file.zip

# 超时控制(连接 5s,总 30s)
curl --connect-timeout 5 --max-time 30 https://api.example.com
```

## 相关笔记
- [[网络API接口]]
- [[curl 命令用法详解]]
- [[MOC-开发编程]]
