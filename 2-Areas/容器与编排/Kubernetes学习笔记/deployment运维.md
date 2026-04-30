# deployment运维

deployment运维相关技术笔记。

```bash
kubectl expose deployment wordpress --selector name=mysql --port=3306 \
--dry-run=client --type=NodePort --protocol tcp -oyaml
#注意生成后的模板还需要自己添加一条nodePort=xxx声明映射到主机的端口号
```

## 系统镜像容器无法启动

k8s使用deploy启动ubuntu、centos等系统镜像时，会出现无法启动。对于像ubuntu这样的系统级docker，用k8s集群启动管理后，会自动关闭，解决方法就是让其一直在运行，所以在yaml文件中增加command命令即可。

```yaml
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["/bin/bash", "-c", "--"]
      args: ["while true; do sleep 30; done;"]
      securityContext:
        privileged: true
```
