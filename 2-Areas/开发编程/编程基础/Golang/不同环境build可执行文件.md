# Go 交叉编译不同平台可执行文件

通过设置 `GOOS` 和 `GOARCH` 环境变量，在一个平台上编译其他平台的可执行文件。

## 编译命令

```bash
# Mac 下编译 Linux/Windows 64位程序
go env -w CGO_ENABLED=0 GOOS=linux GOARCH=amd64
go env -w CGO_ENABLED=0 GOOS=windows GOARCH=amd64

# Linux 下编译 Mac/Windows 64位程序
go env -w CGO_ENABLED=0 GOOS=darwin GOARCH=amd64
go env -w CGO_ENABLED=0 GOOS=windows GOARCH=amd64

# Windows 下编译 Mac/Linux 64位程序
go env -w CGO_ENABLED=0 GOOS=darwin GOARCH=amd64
go env -w CGO_ENABLED=0 GOOS=linux GOARCH=amd64

# 执行编译
go build -o "/root/golang.exe" main.go
```

## 说明

| 参数 | 说明 |
| --- | --- |
| `CGO_ENABLED=0` | 禁用 CGO，确保交叉编译兼容性 |
| `GOOS` | 目标操作系统：`linux`、`darwin`（Mac）、`windows` |
| `GOARCH` | 目标架构：`amd64`（64位）、`arm64`（ARM） |
