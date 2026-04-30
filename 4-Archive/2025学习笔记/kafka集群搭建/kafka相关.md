# Kafka 相关知识

Kafka 消息队列相关技术。

```bash
#启动：
/opt/kafka_2.13-3.6.1/bin/kafka-server-start.sh -daemon /opt/kafka_2.13-3.6.1/config/kraft/server.properties
#关闭：
#/opt/kafka_2.13-3.6.1/bin/kafka-server-stop.sh
#kafka-storage.sh format  --cluster-id BlQPRL0HSdaaMwvx_dVcAQ  --config /opt/kafka_2.13-3.6.1/config/kraft/server.properties --node-id 1
#/var/lib/kafka/meta.properties
#cluster.id=BlQPRL0HSdaaMwvx_dVcAQ
#node.id=1  #对应id号
#version=1

```

# kafka3.6.1配置文件
## kafka01
### /opt/kafka_2.13-3.6.1/config/kraft/server.properties
```yaml
# The role of this server. Setting this puts us in KRaft mode
process.roles=broker,controller

# The node id associated with this instance's roles
node.id=1

# The connect string for the controller quorum
controller.quorum.voters=1@192.168.100.150:9093,2@192.168.100.151:9093,3@192.168.100.152:9093

############################# Socket Server Settings #############################

# The address the socket server listens on.
# Combined nodes (i.e. those with `process.roles=broker,controller`) must list the controller listener here at a minimum.
# If the broker listener is not defined, the default listener will use a host name that is equal to the value of java.net.InetAddress.getCanonicalHostName(),
# with PLAINTEXT listener name, and port 9092.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://kafka01:9092,CONTROLLER://kafka01:9093

# Name of listener used for communication between brokers.
inter.broker.listener.name=PLAINTEXT

# Listener name, hostname and port the broker will advertise to clients.
# If not set, it uses the value for "listeners".
advertised.listeners=PLAINTEXT://kafka01:9092

# A comma-separated list of the names of the listeners used by the controller.
# If no explicit mapping set in `listener.security.protocol.map`, default will be using PLAINTEXT protocol
# This is required if running in KRaft mode.
controller.listener.names=CONTROLLER

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600

############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#log.dirs=/tmp/kraft-combined-logs
log.dirs=/var/lib/kafka

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

```

## kafka02
### /opt/kafka_2.13-3.6.1/config/kraft/server.properties
```properties
# The role of this server. Setting this puts us in KRaft mode
process.roles=broker,controller

# The node id associated with this instance's roles
node.id=2

# The connect string for the controller quorum
controller.quorum.voters=1@192.168.100.150:9093,2@192.168.100.151:9093,3@192.168.100.152:9093

############################# Socket Server Settings #############################

# The address the socket server listens on.
# Combined nodes (i.e. those with `process.roles=broker,controller`) must list the controller listener here at a minimum.
# If the broker listener is not defined, the default listener will use a host name that is equal to the value of java.net.InetAddress.getCanonicalHostName(),
# with PLAINTEXT listener name, and port 9092.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://kafka02:9092,CONTROLLER://kafka02:9093

# Name of listener used for communication between brokers.
inter.broker.listener.name=PLAINTEXT

# Listener name, hostname and port the broker will advertise to clients.
# If not set, it uses the value for "listeners".
advertised.listeners=PLAINTEXT://kafka02:9092

# A comma-separated list of the names of the listeners used by the controller.
# If no explicit mapping set in `listener.security.protocol.map`, default will be using PLAINTEXT protocol
# This is required if running in KRaft mode.
controller.listener.names=CONTROLLER

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600

############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#log.dirs=/tmp/kraft-combined-logs
log.dirs=/var/lib/kafka

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

```

## kafka03
### /opt/kafka_2.13-3.6.1/config/kraft/server.properties
```properties
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

#
# This configuration file is intended for use in KRaft mode, where
# Apache ZooKeeper is not present.
#

############################# Server Basics #############################

# The role of this server. Setting this puts us in KRaft mode
process.roles=broker,controller

# The node id associated with this instance's roles
node.id=3

# The connect string for the controller quorum
controller.quorum.voters=1@192.168.100.150:9093,2@192.168.100.151:9093,3@192.168.100.152:9093

############################# Socket Server Settings #############################

# The address the socket server listens on.
# Combined nodes (i.e. those with `process.roles=broker,controller`) must list the controller listener here at a minimum.
# If the broker listener is not defined, the default listener will use a host name that is equal to the value of java.net.InetAddress.getCanonicalHostName(),
# with PLAINTEXT listener name, and port 9092.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://kafka03:9092,CONTROLLER://kafka03:9093

# Name of listener used for communication between brokers.
inter.broker.listener.name=PLAINTEXT

# Listener name, hostname and port the broker will advertise to clients.
# If not set, it uses the value for "listeners".
advertised.listeners=PLAINTEXT://kafka03:9092

# A comma-separated list of the names of the listeners used by the controller.
# If no explicit mapping set in `listener.security.protocol.map`, default will be using PLAINTEXT protocol
# This is required if running in KRaft mode.
controller.listener.names=CONTROLLER

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600

############################# Log Basics #############################

# A comma separated list of directories under which to store log files
#log.dirs=/tmp/kraft-combined-logs
log.dirs=/var/lib/kafka

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

```

# filebeat配置
filebeat version 7.17.26 (amd64), libbeat 7.17.26 [5b41c60f8a028d1a883be72fb157a1c4ea8aa911 built 2024-11-13 15:21:41 +0000 UTC]

```bash
filebeat.inputs:
- type: log
  # Change to true to enable this input configuration.
  enabled: true
  # Paths that should be crawled and fetched. Glob based paths.
  paths:
    - /usr/local/nginx1/logs/access.log
#==========------------------------------kafka-----------------------------------
output.kafka:
  hosts: ["kafka01:9092","kafka02:9092","kafka03:9093"]
  topic: nginxlog
  keep_alive: 10s

###################### Filebeat Configuration Example #########################

# This file is an example configuration file highlighting only the most common
# options. The filebeat.reference.yml file from the same directory contains all the
# supported options with more comments. You can use it as a reference.
#
# You can find the full configuration reference here:
# https://www.elastic.co/guide/en/beats/filebeat/index.html

# For more available modules and options, please see the filebeat.reference.yml sample
# configuration file.

# ============================== Filebeat inputs ===============================

#filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

# filestream is an input for collecting log messages from files.
#- type: filestream

  # Unique ID among all inputs, an ID is required.
  #  id: my-filestream-id

  # Change to true to enable this input configuration.
  #enabled: false

  # Paths that should be crawled and fetched. Glob based paths.
  #  paths:
  #  - /var/log/*.log
    #- c:\programdata\elasticsearch\logs\*

  # Exclude lines. A list of regular expressions to match. It drops the lines that are
  # matching any regular expression from the list.
  #exclude_lines: ['^DBG']

  # Include lines. A list of regular expressions to match. It exports the lines that are
  # matching any regular expression from the list.
  #include_lines: ['^ERR', '^WARN']

  # Exclude files. A list of regular expressions to match. Filebeat drops the files that
  # are matching any regular expression from the list. By default, no files are dropped.
  #prospector.scanner.exclude_files: ['.gz$']

  # Optional additional fields. These fields can be freely picked
  # to add additional information to the crawled log files for filtering
  #fields:
  #  level: debug
  #  review: 1

# ============================== Filebeat modules ==============================

filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: false

  # Period on which files under path should be checked for changes
  #reload.period: 10s

# ======================= Elasticsearch template setting =======================

setup.template.settings:
  index.number_of_shards: 1
  #index.codec: best_compression
  #_source.enabled: false

# ================================== General ===================================

# The name of the shipper that publishes the network data. It can be used to group
# all the transactions sent by a single shipper in the web interface.
#name:

# The tags of the shipper are included in their own field with each
# transaction published.
#tags: ["service-X", "web-tier"]

# Optional fields that you can specify to add additional information to the
# output.
#fields:
#  env: staging

# ================================= Dashboards =================================
# These settings control loading the sample dashboards to the Kibana index. Loading
# the dashboards is disabled by default and can be enabled either by setting the
# options here or by using the `setup` command.
#setup.dashboards.enabled: false

# The URL from where to download the dashboards archive. By default this URL
# has a value which is computed based on the Beat name and version. For released
# versions, this URL points to the dashboard archive on the artifacts.elastic.co
# website.
#setup.dashboards.url:

# =================================== Kibana ===================================

# Starting with Beats version 6.0.0, the dashboards are loaded via the Kibana API.
# This requires a Kibana endpoint configuration.
setup.kibana:

  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  #host: "localhost:5601"

  # Kibana Space ID
  # ID of the Kibana Space into which the dashboards should be loaded. By default,
  # the Default Space will be used.
  #space.id:

# =============================== Elastic Cloud ================================

# These settings simplify using Filebeat with the Elastic Cloud (https://cloud.elastic.co/).

# The cloud.id setting overwrites the `output.elasticsearch.hosts` and
# `setup.kibana.host` options.
# You can find the `cloud.id` in the Elastic Cloud web UI.
#cloud.id:

# The cloud.auth setting overwrites the `output.elasticsearch.username` and
# `output.elasticsearch.password` settings. The format is `<user>:<pass>`.
#cloud.auth:

# ================================== Outputs ===================================

# Configure what output to use when sending the data collected by the beat.

# ---------------------------- Elasticsearch Output ----------------------------
  # The Logstash hosts
  #hosts: ["localhost:5044"]

  # Optional SSL. By default is off.
  # List of root certificates for HTTPS server verifications
  #ssl.certificate_authorities: ["/etc/pki/root/ca.pem"]

  # Certificate for SSL client authentication
  #ssl.certificate: "/etc/pki/client/cert.pem"

  # Client Certificate Key
  #ssl.key: "/etc/pki/client/cert.key"

# ================================= Processors =================================
processors:
  - add_host_metadata:
      when.not.contains.tags: forwarded
  - add_cloud_metadata: ~
  - add_docker_metadata: ~
  - add_kubernetes_metadata: ~

# ================================== Logging ===================================

# Sets log level. The default log level is info.
# Available log levels are: error, warning, info, debug
#logging.level: debug

# At debug level, you can selectively enable logging only for some components.
# To enable all selectors use ["*"]. Examples of other selectors are "beat",
# "publisher", "service".
#logging.selectors: ["*"]

# ============================= X-Pack Monitoring ==============================
# Filebeat can export internal metrics to a central Elasticsearch monitoring
# cluster.  This requires xpack monitoring to be enabled in Elasticsearch.  The
# reporting is disabled by default.

# Set to true to enable the monitoring reporter.
#monitoring.enabled: false

# Sets the UUID of the Elasticsearch cluster under which monitoring data for this
# Filebeat instance will appear in the Stack Monitoring UI. If output.elasticsearch
# is enabled, the UUID is derived from the Elasticsearch cluster referenced by output.elasticsearch.
#monitoring.cluster_uuid:

# Uncomment to send the metrics to Elasticsearch. Most settings from the
# Elasticsearch output are accepted here as well.
# Note that the settings should point to your Elasticsearch *monitoring* cluster.
# Any setting that is not set is automatically inherited from the Elasticsearch
# output configuration, so if you have the Elasticsearch output configured such
# that it is pointing to your Elasticsearch monitoring cluster, you can simply
# uncomment the following line.
#monitoring.elasticsearch:

# ============================== Instrumentation ===============================

# Instrumentation support for the filebeat.
#instrumentation:
    # Set to true to enable instrumentation of filebeat.
    #enabled: false

    # Environment in which filebeat is running on (eg: staging, production, etc.)
    #environment: ""

    # APM Server hosts to report instrumentation results to.
    #hosts:
    #  - http://localhost:8200

    # API Key for the APM Server(s).
    # If api_key is set then secret_token will be ignored.
    #api_key:

    # Secret token for the APM Server(s).
    #secret_token:

# ================================= Migration ==================================

# This allows to enable 6.7 migration aliases
#migration.6_to_7.enabled: true

```

# nginx 配置
## keepalived.conf
Keepalived v2.2.8 (04/04,2023)

```bash
! Configuration File for keepalived

global_defs {
    router_id LVS_DEVEL
}
vrrp_script chk_nginx {
    script "/etc/keepalived/check_nginx.sh"
    interval 10
    weight -5
    fall 2
    rise 1
}
vrrp_instance VI_1 {
    state MASTER
    # 注意网卡名
    interface ens33
    mcast_src_ip 192.168.100.150
    virtual_router_id 51
    priority 100
    #nopreempt
    advert_int 2
    authentication {
        auth_type PASS
        auth_pass BLUEKING_NGINX_HA
    }
    virtual_ipaddress {
        192.168.100.100/24
    }
    track_script {
      chk_nginx
    }
}

```

## check_nginx.sh
```bash

#!/bin/bash
process_name="nginx"
process_abs_path="/usr/local/nginx1/sbin/nginx"

function is_nginx_running() {
    process_info=$(ps --no-header -C $process_name -o ppid,pid,args | awk '{printf $1 "|" $2 "|" ; for (i=3; i<=NF; i++) { printf "%s ", $i };printf "\n"}' | grep master)
    if [[ -z "$process_info" ]]; then
        return 1
    else
        process_pids=($(echo "$process_info" | awk -F'|' '{print $2}'))
        for _pid in "${process_pids[@]}"; do
            abs_path=$(readlink -f /proc/$_pid/exe)
            if [ "$abs_path" == "$(readlink -f "$process_abs_path")" ]; then
                return 0
            fi
        done
        return 2
    fi
}

err=0
for k in $(seq 1 3); do
    is_nginx_running
    if [[ $? != "0" ]]; then
        err=$((err + 1))
        sleep 1
    else
        err=0
        break
    fi
done

if [[ $err != "0" ]]; then
    exit 1  # 仅返回失败状态，不停止 keepalived
else
    exit 0
fi

```

# python代码
Python 3.10.12

```sql
kafka-python==2.0.2
pymongo==4.13.0
mysql-connector-python==9.3.0
redis==4.5.5
kombu==5.3.1
celery==5.3.1
```

## 消费者mysql-consumer.py
```bash
from kafka import KafkaConsumer
from multiprocessing import Process, current_process
from datetime import datetime
import json
import sys
import re
import mysql.connector
import random
from mysql.connector import Error

# 颜色定义（可选）
class Colors:
    GREEN = '\033[92m'
    BLUE = '\033[94m'
    YELLOW = '\033[93m'
    RED = '\033[91m'
    END = '\033[0m'

# 预定义的随机归属地数据
REGION_DATA = {
    'countries': ['中国', '美国', '日本', '德国', '英国', '法国', '韩国', '俄罗斯', '加拿大', '澳大利亚'],
    'regions': {
        '中国': ['北京', '上海', '广州', '深圳', '杭州', '成都', '武汉', '西安', '南京', '重庆'],
        '美国': ['纽约', '洛杉矶', '芝加哥', '休斯顿', '费城', '凤凰城', '圣安东尼奥', '圣地亚哥', '达拉斯', '圣何塞'],
        '日本': ['东京', '大阪', '名古屋', '横滨', '福冈', '札幌', '神户', '京都', '广岛', '仙台'],
        '德国': ['柏林', '汉堡', '慕尼黑', '科隆', '法兰克福', '斯图加特', '杜塞尔多夫', '多特蒙德', '埃森', '莱比锡']
    },
    'isps': ['中国电信', '中国联通', '中国移动', 'Comcast', 'AT&T', 'Verizon', 'NTT', 'Deutsche Telekom', 'BT Group',
             'Orange']
}

def get_random_geolocation():
    """生成随机归属地信息"""
    country = random.choice(REGION_DATA['countries'])
    region = random.choice(REGION_DATA['regions'].get(country, ['未知地区']))
    isp = random.choice(REGION_DATA['isps'])
    return {
        'country': country,
        'region': region,
        'city': region,  # 简单起见，城市和地区相同
        'isp': isp
    }

def parse_apache_log(log_line):
    pattern = r'^(\S+) \S+ \S+ \[([^$$]+)\] "(\S+) (\S+) (\S+)" (\d+) (\d+) "([^"]*)" "([^"]*)"(?: "([^"]*)")?'
    match = re.match(pattern, log_line)

    if not match:
        return None

    return {
        'ip': match.group(1),
        'timestamp': match.group(2),
        'method': match.group(3),
        'path': match.group(4),
        'protocol': match.group(5),
        'status': int(match.group(6)),
        'size': int(match.group(7)),
        'referer': match.group(8),
        'user_agent': match.group(9)
    }

def insert_log_to_mysql(log_data):
    """将日志数据插入MySQL数据库"""
    try:
        # MySQL连接配置
        connection = mysql.connector.connect(
            host='192.168.100.153',
            database='nginxlog',
            user='root',
            password='123456'
        )
        if connection.is_connected():
            cursor = connection.cursor()
            # 转换时间格式
            log_time = datetime.strptime(log_data['timestamp'], '%d/%b/%Y:%H:%M:%S %z')
            # 生成随机归属地
            geo_info = get_random_geolocation()
            # SQL插入语句
            sql = """
            INSERT INTO access_logs
            (ip, timestamp, method, path, protocol, status, size, referer, user_agent,
             country, region, city, isp)
            VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
            """

            # 执行SQL
            cursor.execute(sql, (
                log_data['ip'],
                log_time,
                log_data['method'],
                log_data['path'],
                log_data['protocol'],
                log_data['status'],
                log_data['size'],
                log_data['referer'],
                log_data['user_agent'],
                geo_info['country'],
                geo_info['region'],
                geo_info['city'],
                geo_info['isp']
            ))

            connection.commit()
            print(f"{Colors.GREEN}[MySQL] 日志插入成功 - IP: {log_data['ip']}{Colors.END}")

    except Error as e:
        print(f"{Colors.RED}[MySQL] 数据库错误: {e}{Colors.END}")
    finally:
        if connection.is_connected():
            cursor.close()
            connection.close()

def format_timestamp():
    """返回格式化的当前时间戳"""
    return datetime.now().strftime("%Y-%m-%d %H:%M:%S.%f")[:-3]

def pretty_print_json(msg):
    """美化打印JSON数据"""
    try:
        json_msg = json.loads(msg)
        return json.dumps(json_msg, indent=4, ensure_ascii=False)
    except json.JSONDecodeError:
        return msg

def consume_kafka_partition(topic, group_id, partition):
    """
    消费指定Kafka分区的消息
    :param topic: 主题名称
    :param group_id: 消费者组ID
    :param partition: 分区号
    """
    consumer = KafkaConsumer(
        group_id=group_id,
        bootstrap_servers=["192.168.100.150:9092", "192.168.100.151:9092", "192.168.100.152:9092"],
        auto_offset_reset="earliest",
        enable_auto_commit=True,
        auto_commit_interval_ms=5000,
        value_deserializer=lambda x: x.decode("utf-8")
    )

    proc_name = current_process().name

    print(f"{Colors.GREEN}[{format_timestamp()}]{Colors.END} "
          f"{Colors.BLUE}{proc_name}{Colors.END} "
          f"开始消费主题 [{topic}] 分区 [{partition}] 的消息")

    consumer.subscribe([topic])

    try:
        for message in consumer:
            print("\n" + "=" * 80)
            print(f"{Colors.GREEN}[{format_timestamp()}]{Colors.END} "
                  f"{Colors.BLUE}{proc_name}{Colors.END} 收到消息:")

            # 结构化输出消息元数据
            print(f"{Colors.YELLOW}主题: {message.topic}")
            print(f"分区: {message.partition}")
            print(f"偏移量: {message.offset}")
            print(f"时间戳: {datetime.fromtimestamp(message.timestamp / 1000).strftime('%Y-%m-%d %H:%M:%S')}")
            print(f"键: {message.key}{Colors.END}")

            # 美化输出消息内容
            print(f"\n{Colors.GREEN}消息内容:{Colors.END}")
            print(pretty_print_json(message.value))
            print(json.loads(message.value)["message"])

            # mysql连接配置
            """============================"""
            # 解析日志
            parsed_log = parse_apache_log(json.loads(message.value)["message"])
            if parsed_log:
                insert_log_to_mysql(parsed_log)
            """============================"""

            print("=" * 80 + "\n")
    except KeyboardInterrupt:
        print(f"{Colors.RED}[{format_timestamp()}] {proc_name} 收到中断信号，正在停止...{Colors.END}")
    except Exception as e:
        print(f"{Colors.RED}[{format_timestamp()}] {proc_name} 发生错误: {str(e)}{Colors.END}")
    finally:
        consumer.close()
        print(f"{Colors.GREEN}[{format_timestamp()}] {proc_name} 消费者已关闭{Colors.END}")

if __name__ == '__main__':
    topic = "nginxlog"
    group_id = "group_1"
    partitions = [0, 1, 2]

    def start_process(target, args):
        """启动消费者进程"""
        p = Process(target=target, args=args)
        p.name = f"Consumer-P{args[2]}"  # 简化的进程名称
        p.start()
        return p

    print(f"{Colors.GREEN}[{format_timestamp()}] 启动消费者进程组 {group_id}{Colors.END}")

    processes = []
    for partition in partitions:
        p = start_process(consume_kafka_partition, (topic, group_id, partition))
        processes.append(p)
        print(f"{Colors.GREEN}[{format_timestamp()}] 已启动分区 {partition} 的消费者进程 (PID: {p.pid}){Colors.END}")

    try:
        for p in processes:
            p.join()
    except KeyboardInterrupt:
        print(f"\n{Colors.RED}[{format_timestamp()}] 收到中断信号，等待子进程结束...{Colors.END}")
        for p in processes:
            p.join()

    print(f"{Colors.GREEN}[{format_timestamp()}] 所有进程已结束，指定分区消费完成{Colors.END}")

```

## 数据库结构SQL
Server version       8.0.41

```sql
-- MySQL dump 10.13  Distrib 8.0.41, for Linux (x86_64)
--
-- Host: localhost    Database: nginxlog
-- ------------------------------------------------------
-- Server version       8.0.41

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8mb4 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `access_logs`
--

DROP TABLE IF EXISTS `access_logs`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `access_logs` (
  `id` int NOT NULL AUTO_INCREMENT,
  `ip` varchar(45) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `timestamp` datetime DEFAULT NULL,
  `method` varchar(10) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `path` varchar(255) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `protocol` varchar(10) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `status` int DEFAULT NULL,
  `size` int DEFAULT NULL,
  `referer` varchar(512) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `user_agent` varchar(512) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `country` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `region` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `city` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL,
  `isp` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
/*!40101 SET character_set_client = @saved_cs_client */;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2025-05-23 16:39:36

```

##

## 定时任务celery_app
/root/kafka-python/celery_app

### __init__.py
```sql
from celery import Celery
app=Celery('tasks')
app.config_from_object('celery_app.celery_redis')
```

### celery_redis.py
```sql
from celery.schedules import crontab
import random

# Redis 作为 Broker（消息队列）
BROKER_URL = 'redis://:123456@192.168.100.153:6379/0'

# Redis 作为结果存储
CELERY_RESULT_BACKEND = 'redis://:123456@192.168.100.153:6379/1'

CELERY_RESULT_SERIALIZER = 'json'  # 结果序列化方案
CELERY_TASK_RESULT_EXPIRES = 60 * 60 * 24  # 任务过期时间
CELERY_TIMEZONE = 'Asia/Shanghai'  # 时区配置
REDIS_PASSWORD = "000000"
CELERY_IMPORTS = (  # 指定导入的任务模块,可以指定多个
    'celery_app.task'
)

CELERYBEAT_SCHEDULE = {
    'celery-app.task.task': {
        'task': 'celery_app.task.test',
        'schedule': crontab(minute='*/1'),
        'args': (random.randint(1,100), random.randint(1,100))
    },
    'celery-app.task.mysql': {
        'task': 'celery_app.task.run_show_mysql',
        'schedule': crontab(minute='*/1'),
    }
}

```

### mysql_mon.py
```sql
import mysql.connector
from datetime import datetime

def check_mysql():
    """检查过去5分钟的流量异常"""
    try:
        # 1. 连接数据库
        conn = mysql.connector.connect(
            host="192.168.100.153",
            user="root",
            password="123456",
            database="nginxlog"
        )
        cursor = conn.cursor(dictionary=True)

        # 2. 查询过去5分钟的关键指标
        queries = {
            'total_requests': "SELECT COUNT(*) AS value FROM access_logs WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 5 MINUTE)",
            'total_traffic': "SELECT SUM(size) AS value FROM access_logs WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 5 MINUTE)",
            'error_requests': "SELECT COUNT(*) AS value FROM access_logs WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 5 MINUTE) AND status >= 400",
            'top_ip': """
                SELECT ip, COUNT(*) AS requests
                FROM access_logs
                WHERE timestamp >= DATE_SUB(NOW(), INTERVAL 5 MINUTE)
                GROUP BY ip
                ORDER BY requests DESC
                LIMIT 1
            """
        }

        results = {}
        for key, query in queries.items():
            cursor.execute(query)
            results[key] = cursor.fetchone()

        # 3. 定义简单阈值
        thresholds = {
            'max_requests': 1000,  # 5分钟内1000请求
            'max_traffic': 1 * 1024 * 1024,  # 5MB
            'max_error_rate': 0.3,  # 30%错误率
            'max_ip_concentration': 0.7  # 单个IP占70%请求
        }

        # 4. 检查异常
        anomalies = []

        # 请求数异常
        if results['total_requests']['value'] > thresholds['max_requests']:
            anomalies.append(f"高请求量: {results['total_requests']['value']}次/5分钟")

        # 流量异常
        if results['total_traffic']['value'] and results['total_traffic']['value'] > thresholds['max_traffic']:
            anomalies.append(f"高流量: {results['total_traffic']['value'] / (1024 * 1024):.1f}MB/5分钟")

        # 错误率异常
        if results['total_requests']['value'] > 0:
            error_rate = results['error_requests']['value'] / results['total_requests']['value']
            if error_rate > thresholds['max_error_rate']:
                anomalies.append(f"高错误率: {error_rate * 100:.1f}%")

        # IP集中度异常
        if results['total_requests']['value'] > 0 and results['top_ip']['requests']:
            ip_concentration = results['top_ip']['requests'] / results['total_requests']['value']
            if ip_concentration > thresholds['max_ip_concentration']:
                anomalies.append(f"IP集中: {results['top_ip']['ip']}占{ip_concentration * 100:.1f}%请求")

        # 5. 返回结果
        if anomalies:
            return True, "\n".join(anomalies)
        return False, "过去5分钟流量正常"

    except mysql.connector.Error as err:
        return False, f"数据库错误: {err}"
    finally:
        if 'conn' in locals() and conn.is_connected():
            cursor.close()
            conn.close()

# 使用示例
if __name__ == '__main__':
    is_anomaly, message = check_mysql()
    print(f"{datetime.now()} - 异常: {is_anomaly}")
    print(message)

```

### smtp_send.py
```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header
from email.utils import formataddr

def send_message(warning_msg):
    # 发件人邮箱
    sender = 'a351719672@163.com'
    # 发件人邮箱授权码
    password = 'TWYLFnMDN7s5FQUv'
    # 收件人邮箱
    receiver = '18574813215@163.com'

    # 邮件内容
    mail_content = warning_msg
    message = MIMEText(mail_content, 'plain', 'utf-8')
    message['From'] = formataddr((str(Header("python app", 'utf-8')), sender))
    message['To'] = formataddr((str(Header("admin", 'utf-8')), receiver))
    message['Subject'] = Header("流量告警", 'utf-8')

    try:
        smtpObj = smtplib.SMTP_SSL("smtp.163.com", 465)
        smtpObj.login(sender, password)
        smtpObj.sendmail(sender, [receiver], message.as_string())
        smtpObj.quit()
        print("邮件发送成功")
    except smtplib.SMTPException as e:
        print("邮件发送失败：", e)

if __name__ == '__main__':
    send_message("测试消息")
```

### task.py
```python
from . import app
from . import mysql_mon
from datetime import datetime
from . import smtp_send

@app.task
def test(a, b):
    print("task test start....")
    result = abs(a) + abs(b)
    print("task test end...")
    return result

@app.task
def run_show_mysql():
    is_anomaly, message = mysql_mon.check_mysql()
    print(f"{datetime.now()} - 异常: {is_anomaly}")
    print(message)
    if is_anomaly:
        smtp_send.send_message(message)
    return is_anomaly, message

```

### bash
start_run_task.sh

```bash
celery -A celery_app worker -l info -c 4
```

start_task.sh

```bash
celery -A celery_app beat
```
