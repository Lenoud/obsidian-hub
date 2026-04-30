# Prometheus 监控体系

完整的 Prometheus 监控部署方案，包含 Prometheus、Grafana、Node Exporter 和 Alertmanager，支持主机监控、Docker 服务发现和邮件告警。

## 准备工作

### 准备挂载目录和文件

```bash
mkdir  grafana_data  prometheus  alertmanager rule
touch alertmanager.yml  prometheus.yml
touch rule/alert.yaml
chmod 755 grafana_data  prometheus  alertmanager rule alertmanager.yml  prometheus.yml

#grafana:  admin/admin
#dashboard-node-exporter: id=8919  or id=16098

#热加载配置 curl -X POST http://localhost:9090/-/reload
```

## 部署

### compose.yaml

```yaml
services:
  promethues:
    container_name: promethues1
    hostname: promethues1
    restart: unless-stopped
    user: root
    ports:
      - 9090:9090
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - /var/run/docker.sock:/var/run/docker.sock
      # 用于配置Docker 服务发现
      - ./rule/:/rule/
      - ./prometheus:/prometheus
      - /etc/localtime:/etc/localtime:ro
    image: prom/prometheus:v2.54.1
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
      - --web.console.libraries=/etc/prometheus/console_libraries
      - --web.console.templates=/etc/prometheus/consoles
      - --storage.tsdb.retention=200h
    networks:
      - monitor
  grafana:
    container_name: grafana1
    hostname: grafana1
    image: grafana/grafana:11.2.2
    restart: unless-stopped
    user: "0"
    ports:
      - 3000:3000
    volumes:
      - ./grafana_data:/var/lib/grafana
      - /etc/localtime:/etc/localtime:ro
    networks:
      - monitor
  node_exporter:
    image: prom/node-exporter:v1.8.2
    container_name: exporter1
    hostname: exporter1
    restart: unless-stopped
    command:
      - --path.rootfs=/host
    ports:
      - 9100:9100
    pid: host
    volumes:
      - /:/host:ro,rslave
      - /etc/localtime:/etc/localtime:ro
    networks:
      - monitor
  alertmanager:
    container_name: alertmanager1
    hostname: alertmanager1
    restart: unless-stopped
    image: prom/alertmanager:v0.27.0
    ports:
      - 9093:9093
    command:
      - --config.file=/etc/alertmanager/alertmanager.yml
      - --storage.path=/alertmanager
    networks:
      - monitor
    volumes:
      - ./alertmanager/:/alertmanager/
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
networks:
  monitor: {}
```

### alertmanager.yml

```yaml
global:  # 全局配置部分

  # 配置告警邮箱
  smtp_smarthost: 'smtp.163.com:465'  # SMTP 服务器地址和端口

  # 163邮件服务器地址
  smtp_from: 'a351719672@163.com'  # 发送邮件的邮箱

  # 发送邮件的邮箱用户名
  smtp_auth_username: 'a351719672@163.com'  # 发送邮件的邮箱用户名

  # 发送邮件的邮箱密码
  smtp_auth_password: 'TWYLFnMDN7s5FQUv'  # 发送邮件的邮箱密码

  smtp_require_tls: false  # 是否需要进行TLS验证

route:  # 路由配置部分
  group_by: ['alertname']  # 根据哪些标签进行分组
  group_wait: 30s  # 等待时间
  group_interval: 5m  # 分组间隔
  repeat_interval: 1h  # 重复发送间隔
  receiver: 'email'  # 接收者配置

receivers:  # 接收者配置部分
  - name: 'email'
    email_configs:
      - to: '351719672@qq.com'  # 接收邮件的邮箱

inhibit_rules:  # 抑制规则配置部分
  - source_match:  # 源匹配
      severity: 'critical'  # 告警严重程度为 critical
    target_match:  # 目标匹配
      severity: 'warning'  # 告警严重程度为 warning
    equal: ['alertname', 'dev', 'instance']  # 匹配条件：告警名称、开发环境、实例
```

### prometheus.yml

```yaml
# Global configuration
global:
  scrape_interval: 15s
  evaluation_interval: 15s

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "/rule/alert.yaml" # The alert rules file is declared here

# Scrape configurations
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
        labels:
          instance: prometheus-container

  - job_name: 'alertmanager'
    static_configs:
      - targets: ['alertmanager:9093']
        labels:
          instance: alertmanager-container

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node_exporter:9100']
        labels:
          instance: docker-server

  - job_name: 'grafana'
    static_configs:
      - targets: ['grafana:3000']
        labels:
          instance: dashboard

  # Docker 服务发现配置
  - job_name: 'docker-custom-network'
    docker_sd_configs:
      - host: unix:///var/run/docker.sock  # Docker 主机地址，使用 Unix socket

    # 标签重命名和筛选配置
    relabel_configs:
      # 1. 只抓取属于特定网络的容器
      - source_labels: [__meta_docker_network_name]
        regex: monitor_monitor  #网络名称，默认值为 monitor_monitor
        action: keep

      # 2. 重新命名实例标签为容器名称
      - source_labels: [__meta_docker_container_name]
        target_label: instance

      #  进一步过滤或添加其它标签（如果需要）
      # - source_labels: [__meta_docker_container_name]
      #   regex: ^myapp.*
      #   action: keep
```

### rule/alert.yaml

```yaml
groups:
- name: Prometheus alert
  rules:
  # 对任何实例超过30s无法联系的情况发出警报
  - alert: 服务告警
    expr: up == 0
    for: 30s
    labels:
      severity: critical
    annotations:
      instance: "{{ $labels.instance }}"
      description: "{{ $labels.job }} 服务已关闭"
```

## 说明

- Prometheus Web UI：`http://<IP>:9090`
- Grafana 仪表板：`http://<IP>:3000`（默认账号 admin/admin）
- Node Exporter：`http://<IP>:9100`
- Alertmanager：`http://<IP>:9093`

### 组件说明

- **Prometheus**：指标采集和存储，数据保留 200 小时
- **Grafana**：数据可视化仪表板，推荐 Node Exporter Dashboard ID：8919 或 16098
- **Node Exporter**：主机系统指标采集
- **Alertmanager**：告警管理，支持邮件通知
- Docker 服务发现：自动发现 `monitor_monitor` 网络中的容器

### 热加载配置

```bash
curl -X POST http://localhost:9090/-/reload
```

## 示例 PromQL 查询

> 打开 Prometheus 的 Web UI 或集成的查询界面（通常位于 `http://<prometheus_host>:9090`）。

### 查看 Alertmanager 的当前状态

```plain
up{job="alertmanager"}
```

### 查看 Docker 网络中所有目标的抓取持续时间

```plain
scrape_duration_seconds{job="docker-custom-network"}
```

### 查看 Grafana 的抓取间隔时间

```plain
scrape_duration_seconds{job="grafana"}
```

### 监控所有目标的可用性

```plain
up
```

### 按实例查看 Node Exporter 的 CPU 使用率

```plain
node_cpu_seconds_total{instance="docker-server"}
```

### Prometheus 自身监控（例如总抓取样本数）

```plain
prometheus_tsdb_head_samples_appended_total{instance="prometheus-container"}
```

### 其它

```shell
# 获取某个时间段内所有的 CPU 使用率
rate(cpu_usage_seconds_total[5m])

# 获取每个节点的 CPU 使用率
rate(node_cpu_seconds_total{mode="user"}[5m])

# 获取某个节点的内存使用率
node_memory_MemTotal_bytes - node_memory_MemFree_bytes

# 获取每秒的 HTTP 请求数
rate(http_requests_total[1m])

# 获取某个服务的 HTTP 请求成功率
rate(http_requests_total{status="200"}[5m]) / rate(http_requests_total[5m])

# 获取某个服务的平均响应时间
rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])

# 获取某个服务的当前连接数
sum(node_netstat_TcpActiveOpens[1m])

# 获取某个服务的所有标签
label_values(http_requests_total, instance)

# 获取每个节点的磁盘空间使用情况
node_filesystem_size_bytes - node_filesystem_free_bytes

# 获取内存使用的百分比
100 * (node_memory_MemTotal_bytes - node_memory_MemFree_bytes) / node_memory_MemTotal_bytes

# 获取每个 Pod 的 CPU 使用情况
sum(rate(container_cpu_usage_seconds_total{pod_name="your_pod_name"}[5m])) by (pod_name)

# 获取某个节点的网络流量
rate(node_network_receive_bytes_total[5m]) + rate(node_network_transmit_bytes_total[5m])

# 获取 CPU 使用量的总和
sum(rate(cpu_usage_seconds_total[5m])) by (instance)

-----------------
## 针对单个 Job 的 PromQL 查询
-----------------
# 获取特定 job 的 CPU 使用率
rate(cpu_usage_seconds_total{job="your_job"}[5m])

# 获取特定 job 的 HTTP 请求数
rate(http_requests_total{job="your_job"}[5m])

# 获取特定 job 的内存使用量
node_memory_MemTotal_bytes{job="your_job"} - node_memory_MemFree_bytes{job="your_job"}

# 获取特定 job 的 HTTP 请求成功率
rate(http_requests_total{job="your_job", status="200"}[5m]) / rate(http_requests_total{job="your_job"}[5m])

# 获取特定 job 的磁盘使用量
rate(node_disk_written_bytes_total{job="your_job"}[5m])

# 获取特定 job 的网络接收流量
rate(node_network_receive_bytes_total{job="your_job"}[5m])

# 获取特定 job 的网络发送流量
rate(node_network_transmit_bytes_total{job="your_job"}[5m])

# 获取特定 job 的 HTTP 请求延迟的 99 百分位
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket{job="your_job"}[5m])) by (le))

# 获取特定 job 的内存使用百分比
100 * (node_memory_MemTotal_bytes{job="your_job"} - node_memory_MemFree_bytes{job="your_job"}) / node_memory_MemTotal_bytes{job="your_job"}

# 获取特定 job 的磁盘空间剩余百分比
(node_filesystem_avail_bytes{job="your_job", mountpoint="/"} / node_filesystem_size_bytes{job="your_job", mountpoint="/"}) * 100

# 获取特定 job 的每秒错误请求数
rate(http_requests_total{job="your_job", status="500"}[5m])

# 获取特定 job 的系统负载
avg(node_load1{job="your_job"}[5m])

# 获取特定 job 的容器内存使用量
container_memory_usage_bytes{job="your_job"}

# 获取特定 job 的容器 CPU 使用量
sum(rate(container_cpu_usage_seconds_total{job="your_job"}[5m])) by (container_name)

# 获取特定 job 的每秒磁盘读取量
rate(node_disk_read_bytes_total{job="your_job"}[5m])
```
