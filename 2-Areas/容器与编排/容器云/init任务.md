# Init Container 初始化任务

使用 Kubernetes Init Container 在主容器启动前执行初始化任务。

Init Container 创建一个空文件，主容器判断文件是否存在，不存在则退出。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: busybox
      imagePullPolicy: IfNotPresent
      command: ["sh", "-c", "if [ -f /root/initialized.txt ]; then echo 'File exists'; else echo 'File does not exist'; exit 1; fi"]
      volumeMounts:
      - name: dirfile
        mountPath: /root/
  initContainers:
    - name: init
      imagePullPolicy: IfNotPresent
      image: busybox
      command: ["touch", "/root/initialized.txt"]
      volumeMounts:
      - name: dirfile
        mountPath: /root/
  volumes:
  - name: dirfile
    hostPath:
      path: /root/init_root
```
