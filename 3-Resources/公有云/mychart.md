# MyChart Helm 模板部署

使用赛项提供的 mychart-0.1.0.tgz 模板包，修改模板代码使外部可通过节点地址进行访问，上传模板并在 kcloud 集群中安装实例。

## 修改 Service 类型

将 values.yaml 中 Service type 修改为 NodePort：

```bash
grep -n 'type' values.yaml
# 40:  type: NodePort

# service:
#   type: NodePort
#   port: 80
```

## 上传模板

```bash
curl --data-binary "@wordpress-13.0.23.tgz" http://10.247.97.1:8080/api/charts
```
