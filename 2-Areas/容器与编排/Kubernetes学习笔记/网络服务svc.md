# 网络服务svc

网络服务svc相关技术笔记。



## 直接绑定
```shell
kubectl expose deployment gitlab --type=NodePort --port=80 --target-port=80 --name=gitlab  --dry-run -oyaml
#ubectl expose命令当前不支持直接指定NodePort端口。
#NodePort端口是由Kubernetes分配的，在指定范围内随机选择的。
#我们需要添加 nodePort: 字段
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80  #svc的IP暴露的端口号
    nodePort: 30888  #nodeIP暴露的端口号
  selector:
    app: gitlab
  type: NodePort

```

## 创建svc
```yaml
kubectl create svc nodeport  gitlab --tcp 80:80 --node-port 30888  --dry-run  -oyaml

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - name: 80-80
    nodePort: 30888
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gitlab
  type: NodePort
```
