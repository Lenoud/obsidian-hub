# homebox内网测速

homebox内网测速相关技术笔记。

```yaml
version: '3.8'

services:
  homebox:
    image: xgheaven/homebox:latest
    container_name: homebox
    hostname: homebox
    restart: always
    ports:
      - "3300:3300"  # 映射主机端口到容器端口
```
