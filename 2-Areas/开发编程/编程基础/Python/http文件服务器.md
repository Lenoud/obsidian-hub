# Python HTTP 文件服务器

使用 Python 内置模块快速启动 HTTP 文件共享服务器。

## 快速启动

```bash
# 在当前目录启动 HTTP 服务，默认端口 8000
python3 -m http.server 30890
```

可选参数：
- `--port`：指定端口
- `--bind ADDRESS`：绑定 IP 地址
- `--cgi`：启用 CGI 支持

```bash
# 指定端口和绑定地址
python3 -m http.server 30890 --bind 0.0.0.0
```

## CGI 模式

```python
from http.server import HTTPServer, CGIHTTPRequestHandler
import os

port = 30890
ipaddr = ""  # 空字符串监听所有网卡
shared_dir = r"F:\桌面\2023-9文档等"

os.chdir(shared_dir)
httpd = HTTPServer((ipaddr, port), CGIHTTPRequestHandler)
print(f"Server is running at http://localhost:{port}")
httpd.serve_forever()
```
