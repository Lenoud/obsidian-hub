# docker_exporter

docker_exporter相关技术笔记。

cadvisor-dashboard-id=11600

## docker的exporter为google/cadvisor容器

```shell
cat >> docker-compose.yaml << EOF

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

EOF
```

## 相关笔记

- [[Prometheus-monitor]]
- [[MOC-Docker]]
