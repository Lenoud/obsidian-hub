# GitLab + Jenkins 集成部署

GitLab + Jenkins 集成部署相关技术笔记。

在 Kubernetes 上部署 GitLab 并与 Jenkins 联动，包含 GitLab 部署、代码推送、RabbitMQ/Nacos/MySQL 中间件部署、Nacos 数据库初始化以及 Jenkinsfile 流水线配置。

## 部署 GitLab

```yaml
#apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-pvc
  namespace: jenkins
spec:
  storageClassName: nfs-storage
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gitlab
  name: gitlab
  namespace: jenkins
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
      nodeName: k8s-master-node1
      containers:
      - image: gitlab/gitlab-ce:latest
        name: gitlab-ce
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
          name: web
        - containerPort: 443
          name: http
        env:
        - name: GITLAB_ROOT_PASSWORD
          value: admin123
        - name: GITLAB_ROOT_EMAIL
          value: 351719672@qq.com
        - name: GITLAB_PORT
          value: "80"
        volumeMounts:
        - name: gitlabdata
          mountPath: /var/opt/gitlab
      volumes:
      - name: gitlabdata
        persistentVolumeClaim:
          claimName: gitlab-pvc
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gitlab
  name: gitlab
  namespace: jenkins
spec:
  ports:
  - name: "web"
    nodePort: 30888
    port: 80
    protocol: TCP
    targetPort: 80
  - name: "https"
    nodePort: 30443
    port: 443
    protocol: TCP
    targetPort: 443
  selector:
    app: gitlab
  type: NodePort
```

## 推送代码到 GitLab

```bash
cd /root/jenkins/cloud-manager
git config --global user.name "Administrator"
git config --global user.email "351719672@qq.com"
git remote remove  origin
git remote add origin http://192.168.100.20:30888/root/cloud-manager.git
git push --set-upstream origin --all
```

## 部署中间件（RabbitMQ / Nacos / MySQL）

```bash
[root@master Jenkins-Slave]# kubectl -n jenkins apply -f manifests/rabbit-deploy.yaml

[root@master Jenkins-Slave]# kubectl -n jenkins apply -f manifests/mysql.yaml
[root@master Jenkins-Slave]# kubectl -n jenkins apply -f manifests/nacos.yaml
```

## 初始化 Nacos 数据库

```bash
kubectl cp /root/jenkins/cloud-manager/cloud_manage_public.sql mysql:/root/

kubectl exec mysql

mysql -uroot -proot  -e "create database cloud_manage_public;use cloud_manage_public;source /root/cloud_manage_public.sql;"

# CoreDNS 配置 hosts 解析
coredns
hosts {
masterip nacos
}

kubectl -n kube-system rollout restart deploy coredns
```

## Jenkinsfile 流水线

```groovy
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
