# K8s 中不同名称空间的 Pod 互访

K8s 中不同名称空间的 Pod 互访相关技术笔记。

在 Kubernetes 中，不同命名空间的 Pod 可以相互访问，但需要使用正确的网络策略和服务发现配置。

## 使用完全限定的域名（FQDN）

你可以使用完全限定的域名（FQDN）来访问其他命名空间中的服务。FQDN 的格式是 `service-name.namespace.svc.cluster.local`。

```bash
http://my-service.other-namespace.svc.cluster.local
```

## 通过 Service 的 Cluster IP

你可以通过 Service 的 Cluster IP 地址来访问其他命名空间中的服务。但请注意，这仅适用于同一集群内的 Pod，不适用于跨集群通信。

```bash
http://cluster-ip-of-service
```
