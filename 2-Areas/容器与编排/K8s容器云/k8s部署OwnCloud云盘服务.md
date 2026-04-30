# k8s部署OwnCloud云盘服务

k8s部署OwnCloud云盘服务相关技术笔记。

Kubernetes 容器编排相关笔记。

【题目10】服务部署--部署OwnCloud服务[1.5分]

在Kubernetes集群default命名空间下完成OwnCloud云盘系统的部署。启动一个名为owncloud的Deployment，包含一个Pod，Pod内包含两个容器owncloud（镜像：owncloud:latest）和mysql（mysql:5.6）。为该Deployment创建一个名为owncloud-svc的Service，以NodePort方式将容器的80端口对外暴露为30003。

完成后提交master节点的用户名、密码和IP地址到答题框。

[root@k8s-master-node1 ~]# ping baidu.com -c 4

PING baidu.com (39.156.66.10) 56(84) bytes of data.

64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=1 ttl=46 time=49.9 ms

64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=2 ttl=46 time=49.6 ms

64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=3 ttl=46 time=49.8 ms

64 bytes from 39.156.66.10 (39.156.66.10): icmp_seq=4 ttl=46 time=49.6 ms

--- baidu.com ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 3004ms

rtt min/avg/max/mdev = 49.618/49.770/49.931/0.308 ms

[root@k8s-master-node1 ~]# docker pull mysql:5.6

[root@k8s-master-node1 ~]# docker pull owncloud:latest

[root@k8s-master-node1 ~]# docker images | grep mysql

mysql                                               5.6        dd3b2a5dcb48   9 months ago    303MB

[root@k8s-master-node1 ~]# docker images | grep owncloud

owncloud                                            latest     327bd201c5fb   3 years ago     618MB

[root@k8s-master-node1 k8s]# cat owncloud-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: owncloud
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: owncloud
  template:
    metadata:
      labels:
        app: owncloud
    spec:
      containers:
      - name: owncloud
        image: owncloud:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
          name: owncloud

      - name: mysql
        image: mysql:5.6
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: '123456'
        - name: MYSQL_DATABASE
          value: owncloud

---
apiVersion: v1
kind: Service
metadata:
  name: owncloud-svc
spec:
  ports:
  - port: 80
    nodePort: 30003
  selector:
    app: owncloud
  type: NodePort
```

[root@k8s-master-node1 k8s]# kubectl apply -f owncloud-deployment.yaml

[root@k8s-master-node1 k8s]# kubectl get -f owncloud-deployment.yaml

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE

deployment.apps/owncloud   1/1     1            1           3m59s

NAME                   TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE

service/owncloud-svc   NodePort   10.96.13.108   <none>        80:30003/TCP   3m59s

[root@k8s-master-node1 k8s]# kubectl get pod

NAME                        READY   STATUS    RESTARTS   AGE

owncloud-7df9bccdd9-bqbld   2/2     Running   0          4m3s

[root@k8s-master-node1 k8s]# netstat -ntpl | grep 30003

tcp        0      0 0.0.0.0:30003           0.0.0.0:*               LISTEN      7183/kube-proxy

去浏览器输入[http://ip:30003](http://ip:30003)访问owncloud
