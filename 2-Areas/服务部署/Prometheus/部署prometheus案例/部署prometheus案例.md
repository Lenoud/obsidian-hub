# 版本信息

版本信息相关技术笔记。
| grafana | 9.4.3 |
| --- | --- |
| prometheus | 2.37.6 |
| node_exporter | 1.5.0 |
| alertmanager | 0.25.0 |

# 软件包下载
```shell
#官方网站
https://prometheus.io/download/
#Prometheus下载
wget https://ghproxy.com/https://github.com/prometheus/prometheus/releases/download/v2.37.6/prometheus-2.37.6.linux-amd64.tar.gz
#alertmanager下载
wget https://ghproxy.com/https://github.com/prometheus/alertmanager/releases/download/v0.25.0/alertmanager-0.25.0.linux-amd64.tar.gz
#node_exporter下载
wget https://ghproxy.com/https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
#grafana下载
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-9.4.3.linux-amd64.tar.gz
```

# 环境配置
```shell
#关闭防火墙
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
sed -i "s/SELINUX=.*/SELINUX=disabled/g" /etc/selinux/config
#创建工作目录
mkdir -p /opt/prometheus/
#创建运行Prometheus服务的用户
useradd -M -s /usr/sbin/nologin prometheus
#递归的将用户和组改为prometheus
chown -R prometheus:prometheus /opt/prometheus
```

# 部署服务
```shell
#解压所有软件包
tar -zxvf alertmanager-0.25.0.linux-amd64.tar.gz -C /opt/prometheus/
tar -zxvf grafana-enterprise-9.4.3.linux-amd64.tar.gz -C /opt/prometheus/
tar -zxvf prometheus-2.37.6.linux-amd64.tar.gz -C /opt/prometheus/
tar -zxvf node_exporter-1.5.0.linux-amd64.tar.gz -C /opt/prometheus/

#重新命名
mv /opt/prometheus/node_exporter-1.5.0.linux-amd64/ /opt/prometheus/node_exporter
mv /opt/prometheus/alertmanager-0.25.0.linux-amd64/  /opt/prometheus/alertmanager
mv /opt/prometheus/grafana-enterprise-9.4.3.linux-amd64/  /opt/prometheus/grafana-enterprise
mv /opt/prometheus/prometheus-2.37.6.linux-amd64/  /opt/prometheus/prometheus
```

# 启动服务
```shell
systemctl daemon-reload
systemctl enable prometheus --now
systemctl enable alertmanager --now
systemctl enable grafana-server --now
systemctl enable node_exporter --now
```

# Prometheus配置
## prometheus.yml
#检查测试./promtool check config prometheus.yml

#热加载配置 curl -X POST [http://localhost:9090/-/reload](http://localhost:9090/-/reload)

```shell
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - localhost:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "alert.yml"
#在下方编写的告警文件在这里声明

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
    # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

  - job_name: 'alertmanager'
    scrape_interval: 15s
    static_configs:
    - targets: ['localhost:9093']

  - job_name: 'node-exporter'
    scrape_interval: 15s
    static_configs:
    - targets: ['localhost:9100']
      labels:
        instance: Prometheus服务器
```

## 告警配置
```shell
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

```shell
groups:
- name: DockerContainers
  rules:
  - alert: Containerkilled
    expr: time() - container_last_seen > 60
    for: 0m
    labels:
      severity: warning
    annotations:
      isummary: "Docker容器被杀死 容器: {{ $labels.instance }}"
      description: "{{ $value }}个容器消失了"

  - alert: ContainerAbsent
    expr: absent(container_last_seen)
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: "无容器 容器: {{ $labels.instance }}"
      description: "5分钟检查容器不存在，值为: {{ $value }}"
```

```shell
groups:
- name: MySQL
  rules:
  - alert: MysqlDown
    expr: mysql_up == 0
    for: 30s
    labels:
      severity: critical
    annotations:
      isummary: "MySQL Down,实例:{{ $labels.instance }}"
      description: "MySQL_exporter连不上MySQL了，当前状态为: {{ $value }}"

  - alert: MysqlTooManyConnections
    expr:  max_over_time(mysql_global_status_threads_connected[1m]) / mysql_global_variables_max_connections * 100 > 80
    for: 2m
    labels:
      severity: warning
    annotations:
      isummary: "Mysql连接数过多告警,实例: {{ $labels.instance }}"
      description: "MySQL连接数>80%,当前值: {{ $value }}"

```

```shell
groups:
- name: Nginx
  rules:
  - alert: NginxDown
    expr: nginx_up == 0
    for: 30s
    labels:
      severity: critical
    annotations:
      isummary: "nginx异常,实例:{{ $labels.instance }}"
      description: "{{ $labels.job }} nginx已关闭"
```

Alertmanager配置中一般会包含以下几个主要部分：

全局配置（global）：用于定义一些全局的公共参数，如全局的SMTP配置，Slack配置等内容。

模板（templates）：用于定义告警通知时的模板，如HTML模板，邮件模板等。

告警路由（route）：根据标签匹配，确定当前告警应该如何处理。

接收人（receivers）：接收人是一个抽象的概念，它可以是一个邮箱也可以是微信，Slack或者Webhook等，接收人一般配合告警路由使用。

抑制规则（inhibit_rules）：合理设置抑制规则可以减少垃圾告警的产生。

具体字段解释：

global 这个是全局设置

resolve_timeout 当告警的状态有firing变为resolve的以后还要呆多长时间，才宣布告警解除。这个主要是解决某些监控指标在阀值边缘上波动，一会儿好一会儿不好。

route 是个重点，告警内容从这里进入，寻找自己应该用那种策略发送出去。

receiver 一级的receiver，也就是默认的receiver，当告警进来后没有找到任何子节点和自己匹配，就用这个receiver。

group_by 告警应该根据那些标签进行分组。

group_wait 同一组的告警发出前要等待多少秒，这个是为了把更多的告警一个批次发出去。

group_interval 同一组的多批次告警间隔多少秒后，才能发出。

repeat_interval 重复的告警要等待多久后才能再次发出去。

routes 也就是子节点了，配置项和上面一样。告警会一层层的找，如果匹配到一层，并且这层的continue选项为true，那么他会再往下找，如果下层节点不能匹配那么他就用区配的这一层的配置发送告警。如果匹配到一层，并且这层的continue选项为false，那么他会直接用这一层的配置发送告警，就不往下找了。

match_re 用于匹配label。此处列出的所有label都匹配到才算匹配。

inhibit_rules这个叫做抑制项，通过匹配源告警来抑制目的告警。比如说当我们的主机挂了，可能引起主机上的服务，数据库，中间件等一些告警，假如说后续的这些告警相对来说没有意义，我们可以用抑制项这个功能，让Prometheus只发出主机挂了的告警。

source_match 根据label匹配源告警。

target_match 根据label匹配目的告警。

equal 此处的集合的label，在源和目的里的值必须相等。如果该集合的内的值再源和目的里都没有，那么目的告警也会被抑制。

# alertmanager配置
热加载配置

curl -X POST [http://localhost:9093/-/reload](http://localhost:9093/-/reload)

![[1696592691097-f8af453d-a4dc-4701-b43f-91e4831003cb.png]]

## alertmanager.yml
![[1731042770384-62e2ff0d-5144-4ecb-9f99-178dde29108f.png]]

TWYLFnMDN7s5FQUv

```shell
global:  # 全局配置部分

  # 配置告警邮箱
  smtp_smarthost: 'smtp.163.com:465'  # SMTP 服务器地址和端口

  # 163邮件服务器地址
  smtp_from: 'a351719672@163.com'  # 发送邮件的邮箱

  # 发送邮件的邮箱用户名
  smtp_auth_username: 'a351719672@163.com'  # 发送邮件的邮箱用户名

  # 发送邮件的邮箱密码
  smtp_auth_password: '<邮箱授权码>'  # 发送邮件的邮箱密码

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

# 注册到systemd
/lib/systemd/system/

```shell
[Unit]
Description=Alert Manager
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecStart=/opt/prometheus/alertmanager/alertmanager \
  --config.file=/opt/prometheus/alertmanager/alertmanager.yml \
  --storage.path=/opt/prometheus/alertmanager/data

Restart=always

[Install]
WantedBy=multi-user.target

```

```shell
[Unit]
Description=Grafana server
Documentation=http://docs.grafana.org
[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=on-failure
ExecStart=/opt/prometheus/grafana/bin/grafana-server \
  --config=/opt/prometheus/grafana/conf/defaults.ini \
  --homepath=/opt/prometheus/grafana
[Install]
WantedBy=multi-user.target
```

```shell
[Unit]
Description=node_exporter
Documentation=https://prometheus.io/
After=network.target
[Service]
User=prometheus
Group=prometheus
ExecStart=/opt/prometheus/node_exporter/node_exporter
Restart=on-failure
[Install]
WantedBy=multi-user.target
#--web.listen-adderss=:9101

#配置认证
#ExecStart=/opt/prometheus/node_exporter/node_exporter  --web.config.file=/opt/prometheus/node_exporter/config.yml
#config.yml
#basic_auth_users:
  ## 当前设置的用户名为admin， 可以设置多个
  #admin: $2y$12$6yR84yKSqoYv3B2D70QAOuqggT0QvdpMp1wUNfLwBo63oLYWc1AYy
  ##这里密码为123456   htpasswd -nBC 12 '' | tr -d ':\n'

#prom.yaml
  # - job_name: 'node-exporter'
  #   scrape_interval: 15s
  #   basic_auth:
  #     username: admin
  #     password: 123456
  #   static_configs:
  #   - targets: ['localhost:9100']
  #     labels:
  #       instance: Prometheus服务器

```

```shell
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=on-failure
ExecStart=/opt/prometheus/prometheus/prometheus \
  --config.file=/opt/prometheus/prometheus/prometheus.yml \
  --storage.tsdb.path=/opt/prometheus/prometheus/data \
  --storage.tsdb.retention.time=60d \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```

## 相关笔记
- [[Prometheus-monitor]]
- [[MOC-服务部署]]
