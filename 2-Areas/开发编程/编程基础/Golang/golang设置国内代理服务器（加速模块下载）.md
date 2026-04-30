# Go 设置国内代理服务器

设置 Go 模块代理以加速国内下载速度。

## 配置方法

```bash
# 开启模块模式
go env -w GO111MODULE=on

# 设置阿里云代理
go env -w GOPROXY=http://mirrors.aliyun.com/goproxy/

# 下载包
go get -u github.com/gin-gonic/gin
```

## 包管理常用命令

| 命令 | 说明 |
| --- | --- |
| `go get -u <包名>` | 下载或更新包 |
| `go clean -i <包名>...` | 清理指定包 |
| `go clean --modcache` | 清理全部包缓存 |
