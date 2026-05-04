# Jenkins CI/CD 部署

在 Kubernetes 集群中部署 Jenkins、GitLab、Docker-in-Docker，配置 CI/CD 流水线实现自动化构建和部署。

导入镜像后修改 config.toml 配置，推送镜像到 Harbor。

```bash
ctr -n k8s.io image tag  docker.io/library/maven:3.6-jdk-8 \
192.168.100.20/library/maven:3.6-jdk-8

ctr -n k8s.io  image push  192.168.100.20/library/maven:3.6-jdk-8 --plain-http \
 --user  admin:Harbor12345

ctr -n k8s.io image tag  docker.io/library/cloud-ui:latest \
192.168.100.20/library/cloud-ui:latest

ctr  -n k8s.io  image push --plain-http   --user admin:Harbor12345 \
192.168.100.20/library/cloud-ui:latest

ctr -n k8s.io image tag  docker.io/library/openjdk:8-jdk-alpine  \
192.168.100.20/library/openjdk:8-jdk-alpine

ctr  -n k8s.io  image push --plain-http   --user  admin:Harbor12345 \
192.168.100.20/library/openjdk:8-jdk-alpine
```

# 创建pvc
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  storageClassName: nfs-storage
```

# 创建sa
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: jenkins-admin
  namespace: default
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins-admin
```

# 创建jenkins-deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: jenkins
  name: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  strategy: {}
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      nodeName: k8s-master-node1
      containers:
      - image: jenkins/jenkins:latest
        name: jenkins
        imagePullPolicy: IfNotPresent
        securityContext:
          privileged: true
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: jenkins
          mountPath: /var/jenkins_home
      serviceAccountName: jenkins-admin
      volumes:
      - name: jenkins
        persistentVolumeClaim:
          claimName: jenkins
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: jenkins
  name: jenkins
spec:
  ports:
  - name: "8080"
    nodePort: 30880
    port: 8080
    protocol: TCP
    targetPort: 8080
  - name: "50000"
    nodePort: 50000
    port: 50000
    protocol: TCP
    targetPort: 50000
  selector:
    app: jenkins
  type: NodePort
```

```yaml
 kubectl cp plugins/ jenkins-688c5bd864-vrs9x:/var/jenkins_home/
```

# 创建docker-in-docker
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: docker-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  storageClassName: nfs-storage
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: docker-dind
  name: docker-dind
spec:
  replicas: 1
  selector:
    matchLabels:
      app: docker-dind
  strategy: {}
  template:
    metadata:
      labels:
        app: docker-dind
    spec:
      containers:
      - image: docker:dind
        securityContext:
          privileged: true
        name: docker
        ports:
        - containerPort: 2375
        env:
        - name: DOCKER_HOST
          value: tcp://0.0.0.0:2375
        - name: DOCKER_TLS_CERTDIR
          value: ""
        volumeMounts:
        - name: docker-data
          mountPath: /var/lib/docker
      volumes:
      - name: docker-data
        persistentVolumeClaim:
          claimName: docker-pvc
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: docker-dind
  name: docker-dind
spec:
  ports:
  - name: "2375"
    port: 2375
    protocol: TCP
    targetPort: 2375
  selector:
    app: docker-dind
  type: ClusterIP
status:
  loadBalancer: {}
```

# 修改Jenkins的配置，添加kubernetes-cloud
![[1701178583352-72ff8541-715d-470b-8b64-0bf34f5f22e2.png]]

可以添加一个自由项目测试是否成功，选择好标签jnlp

**Build Steps:选择执行shell**

```yaml
echo "测试 Kubernetes 动态生成 jenkins slave"
echo "=============kubectl============="
kubectl get pods
echo "==============docker in docker==========="
docker info
```

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
      labels:
        app: gitlab
    spec:
      containers:
      - image: gitlab/gitlab-ce:latest
        imagePullPolicy: IfNotPresent
        name: gitlab-ce
        ports:
        - containerPort: 80
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: "admin123"
        - name: GITLAB_ROOT_EMAIL
          value: "351719672@qq.com"
        - name: GITLAB_PORT
          value: "80"
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gitlab
  name: gitlab
spec:
  ports:
  - name: "80"
    nodePort: 30888
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gitlab
  type: NodePort
```

# 推送代码
```bash
#登录gitlab修改相关配置，创建cloud-manager项目
rm -rf .git
git config --global user.name "Administrator"
git config --global user.email "351719672@qq.com"
git init .
git remote add origin http://192.168.100.20:30888/root/cloud-manager.git
git add .
git commit -m "push"
git push  origin master
```

# 部署nacos和mysql、rabbitmq
```bash
kubectl apply -f mysql.yaml -f  nacos.yaml -f rabbit-deploy.yaml
#拷贝cloud_manage_public.sql到数据库里面
#创建并，导入数据库
#登录nacos，导入 nacos_config.zip，修改cloud_manage_public的ip地址
```

# gitlab编写Jenkinsfile
修改成自己的ip地址

```bash
pipeline {
    agent none
    stages {
        stage('mvn-build') {
            agent { label 'jnlp' }
            steps {
                sh 'cd cloud-manage-common/ && /usr/local/maven/bin/mvn install'
                sh '/usr/local/maven/bin/mvn -B -DskipTests clean package'
                sh 'cd cloud-manage-public && /usr/local/maven/bin/mvn -B -DskipTests clean package'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                sh 'sed -i "s|openjdk:8-jdk-alpine|192.168.100.20/library/openjdk:8-jdk-alpine|g" cloud-manage-public/Dockerfile'
                sh 'docker build -t 192.168.100.20/library/cloud-manage-public:latest -f cloud-manage-public/Dockerfile .'
                sh 'docker login 192.168.100.20 -u admin -p Harbor12345'
                sh 'docker push 192.168.100.20/library/cloud-manage-public:latest'
            }
        }
        stage('cloud-deploy') {
            agent { label 'jnlp' }
            steps {
                sh 'sed -i "s|cloud-manage-public:latest|192.168.100.20/library/cloud-manage-public:latest|g" manifests/cloud-deploy.yaml'
                sh 'sed -i "s|cloud-ui:latest|192.168.100.20/library/cloud-ui:latest|g" manifests/cloud-deploy.yaml'
                sh 'sed -i "s|http://IP:9998|http://192.168.100.20:9998|g" manifests/cloud-deploy.yaml'
                sh 'kubectl apply -f manifests/'
            }
        }
    }
}
```

# 进入jenkins按照要求创建一个项目
添加gitlab的凭证信息，保存构建即可

![[1701179376642-c44ee75b-7908-43f7-b18b-115c2c93cd34.png]]
