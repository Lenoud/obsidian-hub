**七、Promethues**

云梦公司开发了-基于Grafana + Prometheus架构的监控系统，并实现全容器化部署，监控系统架构图如下:

![[1685881237511-97353123-ddfc-4e4c-add1-0db36531da90.png]]

请将Node-Exporter、Alertmanager、 Grafana和Prometheus按照要求进行容器化。

1、 [实操题]容器化部署Node -Exporter (2.5分)

在master节点上编写/root/Monitor/Dockerfile-exporter文件构建monitor-exporter:v1.0镜像，具体要求如下: (需要 用到的软件包: [http://10.24. 1.46/Montortar.gz)](http://10.24.%201.46/Montortar.gz))

(1)基础镜像: centos.centos7.9.2009;

(2)使用二进制包node_exporter-0.181.inux-amd64tar.gz安装node-exporter服务;

(3)开放端口: 9100;

(4)设置服务开机自启。

完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。

[root@master ~]# tar -zxvf Monitor.tar.gz

```powershell
tar -zxvf Monitor.tar.gz
```

[root@master ~]# cd Monitor

```powershell
cd Monitor
```

[root@master Monitor]# docker load -i CentOS_7.9.2009.tar

```powershell
docker load -i CentOS_7.9.2009.tar
```

[root@master Monitor]# cat Dockerfile-exporter

```dockerfile
FROM centos:centos7.9.2009
ADD node_exporter-0.18.1.linux-amd64.tar.gz /opt/
COPY exporter.sh /opt/
EXPOSE 9100
CMD ["/bin/bash","/opt/exporter.sh"]
```

[root@master Monitor]# vim exporter.sh

```bash
#!/bin/bash
cd /opt/node_exporter-0.18.1.linux-amd64
./node_exporter
```

[root@master Monitor]# docker build -t monitor-exporter:v1.0 -f Dockerfile-exporter .

```powershell
docker build -t monitor-exporter:v1.0 -f Dockerfile-exporter .
```

2、[实操题] 容器化部署Alertmanager (2.5分)

在master节点上编写/root/Monitor/Dockerfile-alert文件构建monitor-alert:v1.0镜像，具体要求如下: (需要用到的软件包: [http://10.24. 1.46/Monitortar.gz)](http://10.24.%201.46/Monitortar.gz))

(1)基础镜像: centos.centos7.9.2009;

(2)使用提供的二进制包alertmanager-0.19.0.linux-amd64.tar.gz安装Alertmanager服务;

(3)开放端口: 9093、9094;

(4)设置服务开机自启。

完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。

[root@master Monitor]# cat Dockerfile-alert

```dockerfile
FROM centos:centos7.9.2009
ADD alertmanager-0.19.0.linux-amd64.tar.gz /opt/
COPY alertmanager/alertmanager.yml /opt/alertmanager-0.19.0.linux-amd64/
COPY alert.sh /opt
RUN chmod a+x /opt/alert.sh
EXPOSE 9093 9094
CMD ["/bin/bash","/opt/alert.sh"]
```

[root@master Monitor]# vim alert.sh

```bash
#!/bin/bash
cd /opt/alertmanager-0.19.0.linux-amd64
./alertmanager --config.file="alertmanager.yml"
[root@master Monitor]# cd alertmanager
```

[root@master alertmanager]# cat alertmanager.yml  (复制下面的红色部分)

global:

  resolve_timeout: 5m

  smtp_smarthost: '114463512@.163.com:465'

  smtp_from: 'alert@163.com'

  smtp_auth_username: '114463512@163.com'

  smtp_auth_password: '<邮箱授权码>'

  smtp_require_tls: false

route:

  receiver: 'default'

  group_wait: 10s

  group_interval: 1m

  repeat_interval: 1h

  group_by: ['alertname']

inhibit_rules:

- source_match:

    severity: 'critical'

  target_match:

    severity: 'warning'

  equal: ['alertname', 'instance']

[root@master Monitor]# tar -zxvf alertmanager-0.19.0.linux-amd64.tar.gz

```powershell
tar -zxvf alertmanager-0.19.0.linux-amd64.tar.gz
```

[root@master Monitor]# cd /alertmanager-0.19.0.linux-amd64

```powershell
cd /alertmanager-0.19.0.linux-amd64
```

[root@master alertmanager-0.19.0.linux-amd64]# cat alertmanager.yml

global:

  resolve_timeout: 5m  (把刚刚复制的粘贴到下面的位置)

  smtp_smarthost: '114463512@.163.com:465'

  smtp_from: 'alert@163.com'

  smtp_auth_username: '114463512@163.com'

  smtp_auth_password: '<邮箱授权码>'

  smtp_require_tls: false

route:

  group_by: ['alertname']

  group_wait: 10s

  group_interval: 10s

  repeat_interval: 1h

  receiver: 'web.hook'

receivers:

- name: 'web.hook'

  webhook_configs:

  - url: 'http://127.0.0.1:5001/'

inhibit_rules:

  - source_match:

      severity: 'critical'

    target_match:

      severity: 'warning'

    equal: ['alertname', 'dev', 'instance'] （删掉红色部分）

[root@master alertmanager-0.19.0.linux-amd64]# cp alertmanager.yml ../alertmanager/

```powershell
cp alertmanager.yml ../alertmanager/ && cd ..
```

[root@master alertmanager-0.19.0.linux-amd64]# cd ..

[root@master Monitor]# rm -rf alertmanager-0.19.0.linux-amd64

```powershell
rm -rf alertmanager-0.19.0.linux-amd64
```

[root@master Monitor]# docker build -t monitor-alert:v1.0 -f Dockerfile-alert .

```powershell
docker build -t monitor-alert:v1.0 -f Dockerfile-alert .
```

3、 [实操题]容器化部署Grafana (2.5分)

在master节点上编写/root/Monitor/Dockerfile-grafana文件构建monitor-grafana:v1.0镜像，具体要求如下:

(需要用到的软件包: htpt://10.24. 1.46/Monitor.tar.gz)

(1)基础镜像: centos.centos7.9.2009;

(2)使用提供的二进制包grafana-6 4.1.inux-amd64.tar.gz安装grafana服务; .

(3)开放端口: 3000;

(4)设置nacos服务开机自启。

完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。

[root@master Monitor]# cat Dockerfile-grafana

```dockerfile
FROM centos:centos7.9.2009
ADD grafana-6.4.1.linux-amd64.tar.gz /opt/
COPY grafana.sh /opt/
RUN chmod a+x /opt/grafana.sh
EXPOSE 3000
CMD ["/bin/bash","/opt/grafana.sh"]
```

[root@master Monitor]# cat grafana.sh

```bash
#!/bin/bash
cd /opt/grafana-6.4.1/bin
./grafana-server
```

[root@master Monitor]# docker build -t monitor-grafana:v1.0 -f Dockerfile-grafana .

```powershell
docker build -t monitor-grafana:v1.0 -f Dockerfile-grafana .
```

4、[实操题] 容器化部署Prometheus (2.5分)

在master节点上编写/oot/Monitor/Dockerfile-prometheus文件构建monitor-prometheus:v1.0镜像，具体要求如下: (需 要用到的软件包: htp://10.24. 1.46/Monitor.tar.gz)

(1)基础镜像: centos.centos7.9.2009;

(2)使用提供的二进制包monitor-2. 13.0 linux- amd64 tar.gz安装promethues服务;

(3)编写prometheus.yml文件， 创建3个任务模板: prometheus、 node和alertmanager, 并将该文件拷贝到/data/prometheus/目录下;

(4)开放端口: 9090;

(5)设置服务开机自启。

完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。

[root@master Monitor]# tar -zxvf prometheus-2.13.0.linux-amd64.tar.gz

```powershell
tar -zxvf prometheus-2.13.0.linux-amd64.tar.gz
```

[root@master prometheus-2.13.0.linux-amd64]# cp prometheus.yml ../

```powershell
cp prometheus.yml ../
```

[root@master Monitor]# cat Dockerfile-prometheus

```dockerfile
FROM centos:centos7.9.2009
ADD prometheus-2.13.0.linux-amd64.tar.gz /opt/
RUN mkdir -p /data/prometheus
COPY prometheus.yml /data/prometheus
COPY alert-rules.yml /opt/
COPY prometheus.sh /opt/
RUN chmod a+x /opt/prometheus.sh
EXPOSE 9090
CMD ["/bin/bash","/opt/prometheus.sh"]
```

[root@master Monitor]# cat prometheus.sh

```bash
#!/bin/bash
cd /opt/prometheus-2.13.0.linux-amd64
./prometheus --config.file="/data/prometheus/prometheus.yml"
```

[root@master Monitor]# cat prometheus.yml

# my global config

global:

  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.

  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.

  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration

alerting:

  alertmanagers:

  - static_configs:

    - targets:

      - 192.168.10.50:9093  #改成外网ip

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.

rule_files:

  - /opt/alert-rules.yml   #添加

  # - "first_rules.yml"

  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:

# Here it's Prometheus itself.

scrape_configs:

  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.

  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'

    # scheme defaults to 'http'.

    static_configs:

    - targets: ['192.168.10.50:9090']     #添加如下内容

      labels:

        instance: prometheus

  - job_name: node

    static_configs:

    - targets: ['192.168.10.50:9100']

      labels:

        instance: node-exporter

  - job_name: alertmanager

    static_configs:

    - targets: ['192.168.10.50:9093']

      labels:

        instance: alertmanager

[root@master Monitor]# docker build -t monitor-prometheus:v1.0 -f Dockerfile-prometheus .

```powershell
docker build -t monitor-prometheus:v1.0 -f Dockerfile-prometheus .
```

5、[实操题] 编排部署监控系统(5分)

在master节点上编写/root/Monitor/docker-compose.yaml文件，具体要求如下:

(1)容器1名称: monitor-node; 镜像: monitor-exporter:v1.0; 端口映射: 9100:9100;

(2)容器2名称: monitor-alertmanager; 镜像: monitor-alert:v1.0; 端口映射: 9093:9093、

9094:9094;

(3)容器3名称: monitor-grafana; 镜像: monitor-grafana:v1.0; 端口映射: 3000:3000;

(4)容器4名称: monitor-prometheus; 镜像: monitor-prometheus:v1.0; 端口映射: 9090:9090。

完成后编排部署监控系统，将Prometheus设置为Grafana的数据源，并命名为Prometheus。 然后提交master节点的用户名、密码和IP地址到答题框。

[root@master Monitor]# cat docker-compose.yaml

```yaml
version: "3"
services:
  monitor-node:
    container_name: monitor-node
    image: monitor-exporter:v1.0
    ports:
      - 9100:9100
    restart: always

  monitor-alertmanager:
    container_name: monitor-alertmanager
    image: monitor-alert:v1.0
    depends_on:
      - monitor-node
    ports:
      - 9093:9093
      - 9094:9094
    restart: always

  monitor-grafana:
    container_name: monitor-grafana
    image: monitor-grafana:v1.0
    depends_on:
      - monitor-node
      - monitor-alertmanager
    ports:
      - 3000:3000
    restart: always
  monitor-prometheus:
    container_name: monitor-prometheus
    image: monitor-prometheus:v1.0
    depends_on:
      - monitor-node
      - monitor-alertmanager
      - monitor-grafana
    ports:
      - 9090:9090
    restart: always
```

[root@master Monitor]# docker-compose up -d

1. 在浏览器输入：[http://master_ip:9090访问pro](http://master_ip:9090访问网站)metheus

点击 Alerts出现如下界面：

![[1685881238100-c5961ae2-5cf3-4206-b6f8-a83ca0322b1a.png]]

点击Status下的Rules:

![[1685881238575-9001368e-70bd-42ad-a5bd-9c94816f4dfc.png]]

出现如下界面：

![[1685881238939-44fc82b9-f313-4654-b765-7fc449087cc7.png]]

点击Status下的Targets：

![[1685881239285-c79dad29-850b-4418-a235-94a83f9e0b20.png]]

出现如下界面：

![[1685881239615-37b7ace8-7474-4e8d-bee3-2fb7951bdf91.png]]

2.在浏览器输入：http://master_ip:3000访问网站

账号、密码：admin/admin，然后修改密码登录

![[1685881239941-af661dd8-0f2d-418b-a5e6-205ce7725c48.png]]

![[1685881240275-8045bc3a-95c2-4371-83bf-6ae1609ea0b0.png]]

![[1685881240589-f04618e7-88e0-4085-8e52-fa643634da6c.png]]

做完提交满分
