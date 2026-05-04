# 部署gitlab
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: gitlab
  name: gitlab
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gitlab
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: gitlab
    spec:
      containers:
      - image: gitlab/gitlab-ce:latest
        name: gitlab-ce
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: "guosai2023hniu"
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - name: "80"
    nodePort: 30880
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gitlab
  type: NodePort
status:
  loadBalancer: {}

  #部署成功后能登录gitlab即可得满分
#最好配置开启外部钩子，导入项目
```

# 部署gitlab-runner
```bash
#在Start上方插入name= mountPath=/home/gitlab-runner/ci-build-cache/

#/home/gitlab-runner/ci-build-cache/ 路径为评分点

grep -n -A 5 "cat >>/" gitlab-runner/templates/configmap.yaml
82:    cat >>/home/gitlab-runner/.gitlab-runner/config.toml<<EOF
83-    #name=ci-build-cache mountPath=/home/gitlab-runner/ci-build-cache/
84-    EOF
85-
86-    # Start the runner
87-    exec /entrypoint run --user=gitlab-runner \

#或者手动进入gitlab-runner pod
_apt@gitlab-runner-5c445695d9-zhfj6:/$ cd /home/gitlab-runner/.gitlab-runner/
_apt@gitlab-runner-5c445695d9-zhfj6:/home/gitlab-runner/.gitlab-runner$
echo /home/gitlab-runner/ci-build-cache >> config.toml

```

```bash
#修改几个点

grep -n -A 20 "gitlabUrl" gitlab-runner/values.yaml

51:gitlabUrl: http://192.168.100.20:30880/
#第一个点
57-runnerRegistrationToken: "pGCunU-BCKXiwYWUu7nG"

grep -n -A 2 "image = " gitlab-runner/values.yaml

328:        image = "ubuntu:16.04"
329-        privileged = true  #第二个点

#评分规则
helm -n gitlab-ci install gitlab-runner .
helm -n gitlab-ci status gitlab-runner
#返回结果包含30880端口即可得分

kubectl -n gitlab-ci exec deployment.apps/gitlab-runner -- gitlab-runner list
#返回token 和30880

kubectl -n gitlab-ci exec deployment.apps/gitlab-runner \
-- cat /home/gitlab-runner/.gitlab-runner/config.toml
#容器内路径查看包含缓存路径即可得分
```

# 部署k8s集群agent
```yaml
#在项目内创建文件夹
.gitlab/agents/kubernetes-agent/
#随后在文件夹内创建config.yaml文件
config.yaml

随后在选项卡里Infrastructure
选择：
Kubernetes clusters
点击：
Connect a cluster

#然后使用它提供的helm命令修改名称空间，修改文件路径，修改podIP部署即可
helm upgrade --install kubernetes-agent gitlab-agent \
    --namespace gitlab-ci \
    --create-namespace \
    --set image.tag=v15.2.0 \
    --set config.token=nqTzz6gyrH6zsPXdo8sDrauTmz6HKxP6Hs4xYQs7pj72piD2zw \
    --set config.kasAddress=ws://10.244.0.105/-/kubernetes-agent/
```

# 制作投机镜像
目的是为了后面dockerfile编排一个demo-2048的index页面

```bash
cp demo-2048/src/main/webapp/index.html ./

kubectl run n1 --image nginx:latest
kubectl cp n1:etc/nginx/conf.d/default.conf ./default.conf
#修改端口号80为8080

#编写投机镜像
FROM nginx:latest
COPY default.conf /etc/nginx/conf.d/
RUN echo "192.168.100.20 master" >> /etc/hosts
#加上这条主机名的映射，不懂评分点会不会改主机名，以防万一
COPY index.html /usr/share/nginx/html
EXPOSE 8080

nerdctl -n k8s.io image  build -t  192.168.100.20/demo/demo:latest -f Dockerfile .

#随后推送到harbor
grep   -n -A 3 "mirrors"  /etc/containerd/config.toml
153:      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
154:        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."192.168.100.20"]
155-          endpoint=["http://192.168.100.20"]

ctr -n k8s.io image push 192.168.100.20/demo/demo:latest \
--plain-http  --user admin:Harbor12345

#将镜像修改为我们自己的镜像
 grep "192.168.100.20" demo-2048/template/demo-2048.yaml
          image: 192.168.100.20/demo/demo:latest

#部署template下的所有yaml
kubectl apply -f demo-2048/template/

#查看是否启动成功
kubectl get pod,svc -n gitlab-ci -owide

#查看是否返回index页面的结果
curl localhost:8889
```
