# Prometheus-k8s

Prometheus-k8s相关技术笔记。

## deployment
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus:v2.54.1
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: prometheus-config-volume
              mountPath: /etc/prometheus/prometheus.yml  # 目标文件路径
              subPath: prometheus.yml  # 如果只挂载特定的文件，可以指定 subPath
            - name: prometheus-storage
              mountPath: /prometheus
            - name: rule-directory
              mountPath: /rule/
            - name: local-time
              mountPath: /etc/localtime
              readOnly: true
      volumes:
        - name: prometheus-config-volume
          configMap:
            name: prometheus-config
        - name: prometheus-storage
          hostPath:
            path: /root/monitor/prometheus
            type: Directory
        - name: rule-directory
          hostPath:
            path: /root/monitor/rule
            type: Directory
        - name: local-time
          hostPath:
            path: /etc/localtime
            type: File
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:11.2.2
          ports:
            - containerPort: 3000
          volumeMounts:
            - name: grafana-storage
              mountPath: /var/lib/grafana
            - name: local-time
              mountPath: /etc/localtime
              readOnly: true
      volumes:
        - name: grafana-storage
          hostPath:
            path: /root/monitor/grafana_data
            type: Directory
        - name: local-time
          hostPath:
            path: /etc/localtime
            type: File
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-exporter
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:v1.8.2
          ports:
            - containerPort: 9100
          volumeMounts:
            - name: local-time
              mountPath: /etc/localtime
              readOnly: true
      volumes:
        - name: local-time
          hostPath:
            path: /etc/localtime
            type: File
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alertmanager
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alertmanager
  template:
    metadata:
      labels:
        app: alertmanager
    spec:
      containers:
        - name: alertmanager
          image: prom/alertmanager:v0.27.0
          ports:
            - containerPort: 9093
          volumeMounts:
            - name: alertmanager-config-volume
              mountPath: /etc/alertmanager/alertmanager.yml  # 目标文件路径
              subPath: alertmanager.yml  # 如果只挂载特定的文件，可以指定 subPath
            - name: alertmanager-storage
              mountPath: /alertmanager
            - name: local-time
              mountPath: /etc/localtime
              readOnly: true
      volumes:
        - name: alertmanager-config-volume
          configMap:
            name: prometheus-config
        - name: alertmanager-storage
          hostPath:
            path: /root/monitor/alertmanager
            type: Directory
        - name: local-time
          hostPath:
            path: /etc/localtime
            type: File
---
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
  labels:
    service: prometheus
spec:
  selector:
    app: prometheus
  ports:
    - name: prometheus-port
      protocol: TCP
      port: 9090
      nodePort: 9090
      targetPort: 9090
  type: NodePort
---
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
  labels:
    service: grafana
spec:
  selector:
    app: grafana
  ports:
    - name: grafana-port
      protocol: TCP
      port: 3000
      nodePort: 3000
      targetPort: 3000
  type: NodePort
---
apiVersion: v1
kind: Service
metadata:
  name: node-exporter-service
  labels:
    service: node-exporter
spec:
  selector:
    app: node-exporter
  ports:
    - name: node-exporter-port
      protocol: TCP
      port: 9100
      nodePort: 9100
      targetPort: 9100
  type: NodePort
---
apiVersion: v1
kind: Service
metadata:
  name: alertmanager-service
  labels:
    service: alertmanager
spec:
  selector:
    app: alertmanager
  ports:
    - name: alertmanager-port
      protocol: TCP
      port: 9093
      nodePort: 9093
      targetPort: 9093
  type: NodePort

```

## configmaps
```yaml
apiVersion: v1
data:
  prometheus.yml: |
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
      - job_name: 'prometheus'
        static_configs:
          - targets: ['prometheus-service:9090']
            labels:
              instance: prometheus-container
      - job_name: 'alertmanager'
        static_configs:
          - targets: ['alertmanager-service:9093']
            labels:
              instance: alertmanager-container
      - job_name: 'node-exporter'
        static_configs:
          - targets: ['node-exporter-service:9100']
            labels:
              instance: docker-server
      - job_name: 'grafana'
        static_configs:
          - targets: ['grafana-service:3000']
            labels:
              instance: dashboard
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: prometheus-config

---
apiVersion: v1
data:
  alertmanager.yml: |
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
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: alertmanager-config
```

## 相关笔记
- [[Prometheus-monitor]]
- [[promethues系统监控]]
- [[MOC-Docker]]
- [[MOC-服务部署]]
