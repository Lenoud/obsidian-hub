# 官方docker版本

官方docker版本相关技术笔记。

```yaml
services:
  promethues:
    container_name: promethues
    hostname: promethues
    restart: always
    network_mode: db_bridge
    ports:
      - 9090:9090
    user: root
    volumes:
      - ./prome_root:/root
      - ./conf/prometheus/:/etc/prometheus/
      - ./prometheus:/prometheus
    image: prom/prometheus:v2.54.1
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--storage.tsdb.retention=200h'
    labels:
      createdBy: "mon"

#admin:admin
  grafana:
    container_name: grafana
    image: grafana/grafana:11.2.2
    restart: always
    user: '0'
    network_mode: db_bridge
    ports:
      - 3000:3000
    volumes:
      - ./data:/var/lib/grafana
    labels:
      createdBy: "mon"

  node_exporter:
    image: prom/node-exporter:v1.8.2
    container_name: exporter
    restart: always
    command:
      - '--path.rootfs=/host'
    network_mode: db_bridge
    ports:
      - 9100:9100
    pid: host
    volumes:
      - '/:/host:ro,rslave'
    labels:
      createdBy: "mon"

```
