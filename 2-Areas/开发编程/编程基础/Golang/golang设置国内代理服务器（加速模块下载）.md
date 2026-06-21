# Go 设置国内代理服务器

设置 Go 模块代理(GOPROXY)以加速国内 `go get` / `go mod download` 速度。Go 1.13+ 起原生支持 GOPROXY。

## 配置方法

```bash
# 开启模块模式(Go 1.16+ 默认开启,可省略)
go env -w GO111MODULE=on

# 设置国内代理(推荐 goproxy.cn,七牛云维护)
go env -w GOPROXY=https://goproxy.cn,direct

# 备选:阿里云代理
# go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct

# 验证
go env GOPROXY
# https://goproxy.cn,direct

# 下载包
go get -u github.com/gin-gonic/gin
```

## 主流 Go 代理对比

| 代理 | 维护方 | 地址 | 备注 |
| --- | --- | --- | --- |
| **goproxy.cn** | 七牛云 | `https://goproxy.cn` | 国内最常用,稳定 |
| **goproxy.io** | goproxy.io | `https://goproxy.io` | 备选 |
| **阿里云** | 阿里云 | `https://mirrors.aliyun.com/goproxy/` | 阿里云生态 |
| **官方** | Google | `https://proxy.golang.org` | 国内不可访问 |

> `direct` 表示对代理拉不到的包回退到直连源码仓库(github.com 等)。

## 私有仓库不走代理

如果有私有 Git 仓库(如公司内网 GitLab),设置 `GONOSUMCHECK` / `GONOSUMDB` / `GOPRIVATE` 跳过校验:

```bash
# 私有模块跳过 proxy 和 sumdb 校验
go env -w GOPRIVATE=git.company.com,github.com/myorg/*

# 验证
go env GOPRIVATE
```

## 包管理常用命令

| 命令 | 说明 |
| --- | --- |
| `go get -u <包名>` | 下载或更新包(Go 1.18 前用) |
| `go get <包名>@latest` | 下载最新版 |
| `go get <包名>@v1.2.3` | 下载指定版本 |
| `go mod tidy` | 整理依赖(添加缺失的,删除未用的) |
| `go mod download` | 下载依赖到本地缓存 |
| `go mod vendor` | 把依赖复制到项目 vendor/ 目录 |
| `go clean -modcache` | 清理所有模块缓存 |

## Go 1.17+ 变化

Go 1.17+ 引入了**模块图修剪**(Module Graph Pruning),`go mod tidy` 会同时维护 `go.mod` 的 `require` 和间接依赖。Go 1.18+ 的 `go get` 不再安装可执行文件,安装命令行工具用:

```bash
# Go 1.18+ 安装命令行工具
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest

# 不再用
# go get -u github.com/golangci/golangci-lint/cmd/golangci-lint   # 已弃用
```

## 相关笔记
- [[Linux下载Go环境]]
- [[不同环境build可执行文件]] — 跨平台编译
- [[写GUI程序]] — Go GUI 框架
- [[MOC-开发编程]]

