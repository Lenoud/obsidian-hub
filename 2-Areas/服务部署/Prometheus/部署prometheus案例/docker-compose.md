# docker-compose

docker-compose相关技术笔记。

```yaml
version: '3.0'

services:
  prometheus:
    image: docker.io/bitnami/prometheus:latest
    ports:
      - '9090:9090'
    environment:
      - TZ=Asia/Shanghai
    networks:
      - es_default
  grafana:
    image: docker.io/bitnami/grafana:latest
    ports:
      - '3000:3000'
    environment:
      - 'GF_SECURITY_ADMIN_PASSWORD=root'
      - TZ=Asia/Shanghai

    volumes:
      - grafana_data:/opt/bitnami/grafana/data
    networks:
      - es_default
  node_exporter:
    image: prom/node-exporter:latest
    container_name: node_exporter
    command:
      - '--path.rootfs=/host'
    pid: host
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
    ports:
      - 9100:9100
    volumes:
      - 'node_exporter_data:/host:ro,rslave'
    networks:
      - es_default
networks:
  es_default:
    name: es_default
volumes:
  grafana_data:
    driver: local
  node_exporter_data:
    driver: local

```
