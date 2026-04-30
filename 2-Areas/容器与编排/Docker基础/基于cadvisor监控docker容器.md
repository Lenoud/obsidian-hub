# 基于cadvisor监控docker容器

基于cadvisor监控docker容器相关技术笔记。

```yaml
version: "3.0"
services:
  monitor:
    container_name: cadvisor
    image: google/cadvisor
    restart: always
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    ports:
      - 8080:8080

```
