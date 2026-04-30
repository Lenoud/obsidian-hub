# wget介绍

wget 命令行文件下载工具的使用方法。

### 1. 基本下载
+ `wget [URL]`：下载指定 URL 的文件。

### 2. 后台下载
+ `wget -b [URL]`：在后台下载文件。下载进度会记录在 `wget-log` 文件中。

### 3. 重命名文件
+ `wget -O [filename] [URL]`：将下载的文件保存为指定的文件名。

### 4. 断点续传
+ `wget -c [URL]`：从上次下载中断的位置继续下载。

### 5. 限制下载速度
+ `wget --limit-rate=[rate] [URL]`：限制下载速度，例如 `--limit-rate=200k` 限制为每秒 200 KB。

### 6. 下载多个文件
+ `wget -i [file]`：从文件中读取 URL 列表并下载。

### 7. 下载整个网站
+ `wget -r [URL]`：递归下载整个网站。可以配合其他选项控制深度和范围。

### 8. 指定下载深度
+ `wget -r --level=[depth] [URL]`：设置递归下载的深度。

### 9. 只下载 HTML 文件
+ `wget -r -l1 -H -nd -N -p -k [URL]`：下载 HTML 文件及其资源，但不下载外部链接。

### 10. 只下载指定类型的文件
+ `wget -r -A [filetypes] [URL]`：只下载指定类型的文件，例如 `-A jpg,png`。

### 11. 设置代理
+ `wget -e use_proxy=yes -e http_proxy=[proxy] [URL]`：使用指定的代理服务器下载。

### 12. 不下载 HTTPS 证书验证
+ `wget --no-check-certificate [URL]`：跳过 HTTPS 证书验证。

### 13. 下载时使用用户代理
+ `wget --user-agent="[agent]" [URL]`：设置 HTTP 用户代理字符串。

### 14. 限制下载文件大小
+ `wget --max-filesize=[size] [URL]`：限制下载的文件大小，例如 `--max-filesize=10m`。

### 15. 下载时保存页面结构
+ `wget --mirror [URL]`：创建一个网站的镜像。

### 16. 仅下载文件（跳过 HTML 页面）
+ `wget -r -l1 -H -nd -N -np -k [URL]`：只下载文件，跳过 HTML 页面。

### 17. 设置下载超时
+ `wget --timeout=[seconds] [URL]`：设置连接超时和读取超时，例如 `--timeout=30`。

### 18. 支持 FTP 和 HTTP 验证
+ `wget --user=[username] --password=[password] [URL]`：用于 FTP 和 HTTP 验证。

### 19. 限制最大下载次数
+ `wget --tries=[number] [URL]`：设置下载尝试次数。

### 20. 限制并发连接数
+ `wget --wait=[seconds] [URL]`：设置下载时每次请求之间的等待时间。
