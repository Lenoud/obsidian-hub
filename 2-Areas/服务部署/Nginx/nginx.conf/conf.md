# conf

conf相关技术笔记。

```nginx
# 设置 Nginx 运行的用户
user nginx;

# 设置工作进程数，auto 表示根据 CPU 核心数自动分配
worker_processes auto;

# 错误日志路径和级别
error_log /var/log/nginx/error.log notice;
#debug：记录调试信息，通常只在开发和调试阶段使用。
#info：记录一般性的提示信息。
#notice：记录警告信息，如配置错误或某些模块的警告。
#warn：记录警告信息，但比 notice 级别更加严重，可能需要引起注意。
#error：记录错误信息，如请求无法完成或某些操作失败。
#crit：记录严重错误，可能会导致系统崩溃或无法正常工作。
#alert：记录需要立即采取行动的错误，如磁盘空间不足或内存不足。
#emerg：记录系统崩溃或无法正常工作的错误，这是最高级别的错误。

# 主进程 PID 文件位置
pid /var/run/nginx.pid;

events {
  # 使用 epoll 事件模型
  use epoll;
  # 设置每个 worker 进程可以处理的最大连接数
  worker_connections 1024;
  # 允许一个 worker 进程同时接受多个新连接
  multi_accept on;
}

http {
  # 包含 MIME 类型配置文件
  include /etc/nginx/mime.types;

  # 默认 MIME 类型
  default_type application/octet-stream;

  # 定义日志格式
  log_format main '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" "$http_x_forwarded_for"';

  # 访问日志路径和格式
  access_log /var/log/nginx/access.log main;

  # 开启文件发送功能
  sendfile on;

  # 保持连接的超时时间和请求数
  keepalive_timeout 30;
  keepalive_requests 100;

  # 启用 Gzip 压缩
  gzip on;
  # 指定需要压缩的 MIME 类型
  gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

  # 静态文件缓存设置
  #  location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
  #    expires 30d;
  #    add_header Cache-Control "public";
  #  }   放到server块 这段 Nginx 配置指令是用于设置静态文件缓存的。它匹配所有以 .jpg, .jpeg, .png, .gif, .ico, .css 或 .js 结尾的 URL，然后对它们启用 30 天的缓存和公共缓存控制头

  # 包含其他配置文件
  include /etc/nginx/conf.d/*.conf;
}

```
