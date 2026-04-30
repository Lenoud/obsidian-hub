# Linux 安装 Go 环境

在 Linux 系统上下载并安装 Go 语言开发环境。

## 安装步骤

```bash
# 下载 Go 压缩包（可到 https://go.dev/dl/ 选择版本）
wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz

# 解压到 /usr/local 目录
rm -rf /usr/local/go && tar -C /usr/local -zxf go1.20.5.linux-amd64.tar.gz

# 临时写入环境变量
export PATH=$PATH:/usr/local/go/bin

# 验证安装
go version

# 永久写入环境变量
echo "export PATH=$PATH:/usr/local/go/bin" >> /etc/profile
source /etc/profile
```

## 说明

| 步骤 | 说明 |
| --- | --- |
| 解压目录 | `/usr/local/go` |
| 环境变量 | `PATH` 中添加 `/usr/local/go/bin` |
| 验证 | `go version` 输出版本号即安装成功 |
