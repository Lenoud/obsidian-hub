# windows_exporter(Prometheus Windows 监控)

windows_exporter(原名 wmi_exporter)是 Prometheus 官方社区维护的 Windows 系统指标导出器,功能对应 Linux 上的 node_exporter,通过 WMI 或 Windows API 采集 CPU / 内存 / 磁盘 / 网络 / 服务 / IIS / MSSQL 等指标。

## 安装

### 方式一:MSI 安装包(推荐)

1. 下载最新 MSI:<https://github.com/prometheus-community/windows_exporter/releases>
2. 选 `windows_exporter-<version>-amd64.msi`(64 位)
3. 双击安装,默认服务名 `windows_exporter`
4. 默认监听 `:9182`

### 方式二:命令行

```powershell
# 下载
Invoke-WebRequest -Uri "https://github.com/prometheus-community/windows_exporter/releases/download/v0.26.0/windows_exporter-0.26.0-amd64.msi" -OutFile "windows_exporter.msi"

# 静默安装
msiexec /i windows_exporter.msi /quiet

# 启动服务
Start-Service windows_exporter
```

## 验证

```text
http://<windows-ip>:9182/metrics
http://<windows-ip>:9182/                  # 主页(状态信息)
```

应输出大量 `windows_` 开头的指标。

## Prometheus 配置

```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'windows'
    static_configs:
      - targets: ['192.168.1.50:9182', '192.168.1.51:9182']
```

## Grafana Dashboard

推荐直接用社区做好的仪表盘:

- **Dashboard ID: 12422**(Windows Node):<https://grafana.com/grafana/dashboards/12422>
- Dashboard ID: 15392(更详细)

在 Grafana 里:Dashboards → Import → 输入 ID → 选 Prometheus 数据源。

## 自定义采集器(可选)

默认开启的采集器:cpu、cs(Computer System)、memory、net、os、service、system、logon、thermalzone

通过启动参数或注册表自定义:

```text
# 启动参数(注册表 HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\windows_exporter)
--collectors.enabled=cpu,cs,memory,net,os,service,system,iis,mssql

# 常用额外采集器:
# iis          IIS 指标
# mssql        SQL Server
# msmq         消息队列
# dns          DNS 服务器
# exchange     Exchange
```

## 常见问题

| 问题 | 排查 |
| --- | --- |
| Prometheus 抓取超时 | Windows 防火墙挡了 9182 端口;`netsh advfirewall firewall add rule name="windows_exporter" dir=in action=allow protocol=TCP localport=9182` |
| WMI 查询慢导致 exporter 卡 | 减少采集器(去掉 mssql/exchange 等重的) |
| 内存占用高 | 已知问题,升级到 0.20+ |
| 服务启动失败 | 看 `C:\Program Files\windows_exporter\` 下的日志 |

## 资源

- GitHub:<https://github.com/prometheus-community/windows_exporter>
- Dashboard 12422:<https://grafana.com/grafana/dashboards/12422-windows-node>
- 官方指标参考:<https://github.com/prometheus-community/windows_exporter/blob/master/docs/collector.md>

## 相关笔记
- [[node_exporter]] — Linux 版本
- [[promethues系统监控]] — Prometheus 总览
- [[Prometheus-k8s]] — K8s 上部署 Prometheus
- [[MOC-服务部署]]
