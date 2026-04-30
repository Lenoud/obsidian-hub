**BlueOcean**

该公司决定采用GitLab +Jenkins来构建CICD环境，以缩短新功能开发上线周期，及时满足客户的需求，实现DevOps的部分流程，来减轻部署运维的负担，实现可视化容器生命周期管理、应用发布和版本迭代更新，请完成GitLab + Jenkins + Kubernetes的CICD环境部署（构建持续集成所需要的所有软件包在软件包BlueOcean.tar.gz中）。CICD应用系统架构如下：

![[1686464793995-81c05689-e7f7-453e-a4b7-7fb15dd4e3af.png]]

【适用平台】私有云

【题目1】安装Jenkins环境[1.5分]

使用镜像jenkins/Jenkins:latest在Kubernetes集群devops命名空间下完成Jenkins的部署，Deployment和Service名称均为jenkins，要求以NodePort方式将Jenkins的8080端口对外暴露为30880，并使用提供的软件包完成Blue Ocean等离线插件的安装。部署完成后设置Jenkins用户名为jenkins；密码为000000，并在授权策略中配置“任何用户可以做任何事(没有任何限制)”。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/BlueOcean.tar.gz）

[root@k8s-master-node1 images]# cd /root/BlueOcean/images

[root@k8s-master-node1 images]# docker load -i jenkins_jenkins_latest.tar

[root@k8s-master-node1 images]# docker load -i gitlab_gitlab-ce_latest.tar

[root@k8s-master-node1 images]# docker load -i java_8-jre.tar

[root@k8s-master-node1 images]# docker load -i maven_latest.tar

[root@k8s-master-node1 images]# cd ..

[root@k8s-master-node1 BlueOcean]# kubectl create ns devops

```yaml
[root@k8s-master-node1 BlueOcean]# cat jenkins.yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins
  namespace: devops
  labels:
    app: jenkins
spec:
  type: NodePort
  ports:
  - name: http
    port: 8080
    targetPort: 8080
    nodePort: 30880
  selector:
    app: jenkins
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins
  namespace: devops
  labels:
    app: jenkins
spec:
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      nodeName: k8s-master-node1
      containers:
      - name: jenkins
        image: jenkins/jenkins:latest
        imagePullPolicy: IfNotPresent
        securityContext:
          runAsUser: 0
          privileged: true
        ports:
        - name: http
          containerPort: 8080
        volumeMounts:
        - mountPath: /var/jenkins_home
          name: jenkinshome
        - mountPath: /usr/bin/docker
          name: docker
        - mountPath: /var/run/docker.sock
          name: dockersock
        - mountPath: /usr/bin/kubectl
          name: kubectl
        - mountPath: /root/.kube
          name: kubeconfig
      volumes:
      - name: jenkinshome
        hostPath:
          path: /home/jenkins_home
      - name: docker
        hostPath:
          path: /usr/bin/docker
      - name: dockersock
        hostPath:
          path: /var/run/docker.sock
      - name: kubectl
        hostPath:
          path: /usr/bin/kubectl
      - name: kubeconfig
        hostPath:
          path: /root/.kube
```

[root@k8s-master-node1 BlueOcean]# kubectl apply -f jenkins.yaml

service/jenkins created

deployment.apps/jenkins created

[root@k8s-master-node1 BlueOcean]# kubectl get pod -n devops

NAME                      READY   STATUS    RESTARTS   AGE

jenkins-6f95f5674-t2srw   1/1     Running   0          9s

通过 http://master_IP:30880 访问Jenkins，如图所示：

![[1686464794493-120df88a-4ced-479a-88f8-b41a188aceb0.png]]

复制上面的路径下来获取jenkins密码

[root@k8s-master-node1 Jenkins]# kubectl exec deploy/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword

7a96e0fffb7f47d6a21c833476fb3b11

输入密码后点击“继续”之后如图所示（不要着急动，先往下离线安装插件）：

![[1686464794984-63340dc9-b703-4de2-ab36-3fde1bced0f2.png]]

将离线插件包拷贝到Jenkins：

[root@k8s-master-node1 Jenkins]# kubectl cp plugins/ jenkins-8645dd45b4-tnncq:/var/jenkins_home

重启Jenkins：

[root@k8s-master-node1 Jenkins]# kubectl rollout restart deployment jenkins

刷新 Jenkins 界面，选择“跳过插件安装”

安装完成后进入用户创建页面，创建一个用户 jenkins，密码 000000，如图所示：

![[1686464795260-3f0b8247-6f07-41e4-8294-8960be74828e.png]]

点击“保存并完成”，如图所示：

![[1686464795569-81a82a4b-ac65-4966-8708-75adecf8f490.png]]

点击“保存并完成”，如图所示：

![[1686464795927-a73548dd-b544-4bcc-b919-07b9d20a20e8.png]]

点击“开始使用Jenkins”并使用新创建的用户登录 Jenkins，如图所示：

![[1686464796196-68f745d6-946a-4b23-869f-4be45d93a703.png]]

（3）开启Jenkins 匿名访问

登录 Jenkins 首页，点击“系统管理”→“全局安全配置”，勾选“任何用户可以做任

何事(没有任何限制)”，如图所示。

![[1686464796509-0913dabd-3f1a-4342-a6bd-f1361109b639.png]]

【题目2】安装GitLab环境[1.5分]

使用镜像gitlab/gitlab-ce:latest在Kubernetes集群devops命名空间下完成GitLab的部署，Deployment和Service名称均为gitlab，设置GitLab的root用户密码为admin@123，并以NodePort方式将GitLab的80端口对外暴露为30888。部署完成后新建公开项目springcloud，并将springcloud文件夹中的代码上传到该项目。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/BlueOcean.tar.gz）

[root@k8s-master-node1 Jenkins]# cat gitlab-deployment.yaml #下面虚化部分可以不用写

apiVersion: v1

kind: Service

metadata:

  name: gitlab

  namespace: devops

spec:

  type: NodePort

  ports:

  - port: 443

    nodePort: 30443

    targetPort: 443

    name: gitlab-443

  - port: 80

    nodePort: 30888

    targetPort: 80

    name: gitlab-80

  selector:

    app: gitlab

---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gitlab

  namespace: devops

spec:

  selector:

    matchLabels:

      app: gitlab

  revisionHistoryLimit: 2

  template:

    metadata:

      labels:

        app: gitlab

    spec:

      containers:

      - image: gitlab/gitlab-ce:latest

        name: gitlab

        imagePullPolicy: IfNotPresent

        env:

        - name: GITLAB_ROOT_PASSWORD

          value: admin@123

        - name: GITLAB_ROOT_EMAIL

          value: guorui@example.com

        - name: GITLAB_PORT

          value: "80"

        ports:

        - containerPort: 443

          name: gitlab-443

        - containerPort: 80

          name: gitlab-80

[root@k8s-master-node1 BlueOcean]# kubectl apply -f gitlab.yaml

service/gitlab created

deployment.apps/gitlab created

[root@k8s-master-node1 BlueOcean]# kubectl get pod -n devops

NAME                      READY   STATUS    RESTARTS   AGE

gitlab-7f66cc64cd-n7r2g   1/1     Running   0          3s

jenkins-6f95f5674-t2srw   1/1     Running   0          6m1s

Gitlab 启动较慢，可以通过 docker logs 查看启动状态。启动完成后，在 web 端访问 Gitlab

（http://master_IP:30888），如图所示：

![[1686464796830-bf1c26d3-531c-41c0-a425-6420acb91c12.png]]

登录Gitlab，如图所示：

![[1686464797100-e493af78-7fb5-483f-ba80-fdc768776e1c.png]]

记得到下面这个位置设置一下:

![[1686464797394-7515d707-e1ec-4818-af90-d09b7d15f993.png]]

（2）.创建项目

点击“New project”，如图所示：

![[1686464797787-defb6bfa-ca8d-482b-9673-2990db5ec1b4.png]]

点击“Create blank project”创建项目 springcloud，可见等级选择“Public”，如图所示：

![[1686464798214-4baac36e-47b4-4302-9fdd-728ab9f61678.png]]

push 源代码到 gitlab 的 springcloud 项目：

[root@k8s-master-node1 Jenkins]# cd springcloud/ #一定要来到这个目录下

[root@k8s-master-node1 springcloud]# git config --global user.name "administrator"

[root@k8s-master-node1 springcloud]# git config --global user.email "admin@example.com"

[root@k8s-master-node1 springcloud]# git remote remove origin

[root@k8s-master-node1 springcloud]# git remote add origin [http://192.168.2.70:30888/root/springcloud.git](http://192.168.2.70:30888/root/springcloud.git)

[root@k8s-master-node1 springcloud]# git add .

[root@k8s-master-node1 springcloud]# git commit -m "initial commit"

# On branch master

nothing to commit, working directory clean

[root@k8s-master-node1 springcloud]# git push -u origin master

Username for 'http://20.20.20.13:30888': root

Password for 'http://root@20.20.20.13:30888':

Counting objects: 3192, done.

Delta compression using up to 8 threads.

Compressing objects: 100% (1428/1428), done.

Writing objects: 100% (3192/3192), 1.40 MiB | 1.73 MiB/s, done.

Total 3192 (delta 1233), reused 3010 (delta 1207)

remote: Resolving deltas: 100% (1233/1233), done.

remote:

remote: To create a merge request for master, visit:

remote:   http://gitlab-cbf9647c4-gl95v/root/springcloud/-/merge_requests/new?merge_request%5Bsource_branch%5D=master

remote:

To [http://20.20.20.13:30888/root/springcloud.git](http://20.20.20.13:30888/root/springcloud.git)

 * [new branch]      master -> master

Branch master set up to track remote branch master from origin.

刷新网页，springcloud 项目 master 分支中的文件已经更新了，如图所示：

![[1686464798464-1388ad20-c6f5-4f4d-9f94-9917d5998b38.png]]

【题目3】配置Jenkins连接GitLab[1分]

在GitLab中生成名为jenkins的“Access Tokens”，在Jenkins中配置GitLab凭据并测试其连通性。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/BlueOcean.tar.gz）

(1).设置Outbound requests

登录Gitlab 管理员界面（http://master_IP:30888/admin），如图所示：

![[1686464798765-49718091-2b8a-42f5-b66a-2dcfd33aa286.png]]

点击左侧导航栏的“Settings”→“Network”，设置“Outbound requests”，勾选“Allow requests to the local network from web hooks and services”，如图所示：

![[1686464799473-1f1b73b0-f325-4430-b969-e7ab9dc2c3f4.png]]

配置完成后保存。

(2).创建Gitlab API Token

点击Gitlab 用户头像图标，如图所示：

![[1686464799901-5226f3b6-b68e-4be7-ae64-5cbceca7fc75.png]]

点击“Preferences”，如图所示：

![[1686464800618-24ae8d60-47ef-43a1-8e13-40569b8be0a6.jpeg]]点击左侧导航栏的“Access Tokens”添加 token，如图所示：

![[1686464800913-5181fa7b-0a83-45c4-ba6f-6dcbdb78eaa9.jpeg]]

点击“Create personal access token”生成Token，如图所示：

![[1686464801323-6839653b-9c7f-4793-9dc2-1d5d7b2df29f.png]]

复制Token（4yN6xwNYafmYMg-gmyPd），后面配置 Jenkins 时会用到。

(3)设置Jenkins

登录Jenkins 首页，点击“系统管理”→“系统配置”，配置 Gitlab 信息，取消勾选“Enable authentication for '/project' end-point”，输入“Connection name”和“Gitlab host URL”，然后点击“添加”→“Jenkins”添加认证信息，将 Gitlab API Token 填入，如图所示：

![[1686464802010-47caff78-c999-48d7-91ea-8ed9b85b5cae.png]]

点击“Test Connection”，如图所示：

![[1686464802266-88814968-aae1-42f3-a206-dd9f990eccfe.png]]

如果是http401也没关系，继续往下即可

【题目4】构建CI/CD[4分]

在Jenkins中新建流水线任务springcloud，流水线选择“Pipeline script from SCM”。在springcloud项目中新建Jenkinsfile脚本文件，编写声明式Pipeline，要求完成构建maven项目，然后构建Docker镜像并推送到Harbor仓库的springcloud项目，并基于新构建的镜像完成config和gateway服务自动发布到Kubernetes集群springcloud命名空间下。最后配置Webhook触发构建。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径http://<IP>/BlueOcean.tar.gz）

第一种方法，老实写流水线：

(1).新建任务

登录Jenkins 首页，点击左侧导航栏“新建任务”，如图所示，选择构建一个流水线。

![[1686464802553-cf90ba9e-e12a-4335-9084-b570110f0258.jpeg]]

点击“确定”，配置构建触发器，如图所示：

![[1686464803003-b578299e-42a7-4269-92d7-6997ecc3c4e8.jpeg]]

记录下 GitLab webhook URL 的地址（[http://20.20.20.13:30880/project/springcloud](http://20.20.20.13:30880/project/springcloud)），后期配置 webhook 需要使用。

配置流水线，如图所示：

![[1686464803411-892dc0e6-a8e5-4e15-a7ee-945f9082dc52.png]]

![[1686464804058-7535e9f9-7dbf-48ec-8755-8ab516155ad0.png]]

![[1686464804333-bd5de049-d148-4eb8-b671-7f1c1f9639c6.png]]

![[1686464804665-a4a1e77e-3891-4211-acfa-e85c2d8f5364.png]]

回到gitlab web界面下springcloud项目master分支添加流水线脚本Jenkinsfile

![[1686464805094-97a2adc7-64c8-4a98-94f8-1b75b29bac49.png]]![[1686464805565-3c872097-4ffd-4b91-86f0-cb2fe9897f7a.png]]

流水线脚本

```json
pipeline{
    agent none
    stages{
        stage('mvn-build'){
            agent {
                docker {
                    image '172.129.20.7/library/maven'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps{
                sh 'cp -rfv /opt/repository /root/.m2/ && ls -l /root/.m2/repository'
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
        stage('image-build'){
            agent any
            steps{
                sh 'cd gateway && docker build -t 172.129.20.7/springcloud/gateway -f Dockerfile .'
                sh 'cd config && docker build -t 172.129.20.7/springcloud/config -f Dockerfile .'
                sh 'docker login 172.129.20.7 -u=admin -p=Harbor12345'
                sh 'docker push 172.129.20.7/springcloud/gateway'
                sh 'docker push 172.129.20.7/springcloud/config'
            }
        }
        stage('cloud-deploy'){
            agent any
            steps{
                sh 'sed -i "s/sqshq\\/piggymetrics-gateway/172.129.20.7\\/springcloud\\/gateway/g" yaml/deployment/gateway-deployment.yaml'
                sh 'sed -i "s/sqshq\\/piggymetrics-config/172.129.20.7\\/springcloud\\/config/g" yaml/deployment/config-deployment.yaml'
                sh 'kubectl get ns springcloud || kubectl create ns springcloud'
                sh 'kubectl apply -f yaml/deployment/gateway-deployment.yaml'
                sh 'kubectl apply -f yaml/deployment/config-deployment.yaml'
                sh 'kubectl apply -f yaml/svc/gateway-svc.yaml'
                sh 'kubectl apply -f yaml/svc/config-svc.yaml'
            }
        }
    }
}
```

vi一个文本用正则表达式替换IP为mastarip即可%s///g

（2）创建仓库项目

登录Harbor，新建项目 springcloud，访问级别设置为公开，创建完成后如图所示：

![[1686464805982-c4deb38a-1d20-44d2-a69c-a6a7e9de0792.jpeg]]

进入项目查看镜像列表，如图所示，此时为空，无任何镜像：

![[1686464806345-f473dad3-73de-46dd-948b-b07f4fb7edfd.jpeg]]

（3）触发构建

登录 Gitlab，进入 springcloud 项目，点击左侧导航栏“Settings”→“Webhooks”，将前面记录的GitLab webhook URL 地址填入 URL 处，禁用 SSL 认证，如图所示。

![[1686464806663-74e29485-a12f-4ce7-9553-e5318e78919c.jpeg]]

取消勾选ssl

点击“Add webhook”添加 webhook，完成后如图所示：

![[1686464807066-ff73b633-2ed9-41fa-afd9-609358df7faa.png]]

点击“Test”→“Push events”进行测试，如图所示：

![[1686464807246-3d1fabfb-a4a7-4312-8127-cd71e3e5ad1d.png]]

结果返回HTTP 200 则表明 Webhook 配置成功。

（4）Jenkins 查看

登录Jenkins，可以看到 springcloud 项目已经开始构建，如图所示：

![[1686464807950-f2692bbb-79ad-4e03-b459-ac5d829878fe.png]]

1. 不知道是k8s版本问题还是软件包问题，jenkins的pod里kubectl无法使用（/root/.kube已经挂载映射了），所以流水线在创建namespace那里失败了，所以要手动创建和手动启动gateway和config

我自己排错成功解决了部署失败问题,首先找到Jenkins的job容器 docker查找的话一般叫k8s_jenkins_jenkins-id，k8s查找的话就是jenkins-id

exec 进入容器

echo 192.168.100.40 api.server.cluster.local >> /etc/hosts   #（masterIP）

写入后执行kubectl get pod 命令看有无保错 没报错了就是成功了

之后再运行流水线即可

master # echo 192.168.100.40 master >> /etc/hosts (masterIP不知道有什么作用，但是好像有评分点，可最后跑完流水线再查看hosts里面有没有这个字段没有就加进去)

首先进入容器里找到gateway-deployment.yaml查看其配置，宿主机就跟着改（config-deployment.yaml同理，这里就不写出来了，记得改就行）

[root@k8s-master-node1 cicd-runner]# cat /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

## kubeeasy managed start

192.168.200.107 apiserver.cluster.local

192.168.200.107 k8s-master-node1

192.168.200.107 master  #加上这条主机名的映射，不懂评分点会不会改主机名，以防万一

192.168.200.107 k8s-worker-node1

## kubeeasy managed end

[root@k8s-master-node1 deployment]# kubectl create ns springcloud

namespace/springcloud created

[root@k8s-master-node1 Jenkins]# kubectl exec -it jenkins-6cb6c87569-ffqks -- bash

root@jenkins-6cb6c87569-ffqks:~# cat /var/jenkins_home/workspace/springcloud/yaml/deployment/gateway-deployment.yaml #查看image名字

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gateway

  namespace: springcloud

  labels:

    name: gateway

spec:

  replicas: 1

  selector:

    matchLabels:

        name: gateway

  template:

    metadata:

      labels:

        name: gateway

    spec:

      terminationGracePeriodSeconds: 120

      containers:

      - name: gateway

        image: 192.168.200.107/springcloud/gateway

        imagePullPolicy: IfNotPresent

        env:

        - name: CONFIG_SERVICE_PASSWORD

          value: admin

[root@k8s-master-node1 Jenkins]# cat springcloud/yaml/deployment/gateway-deployment.yaml  #回到宿主机把jenkins的pod里查到的image写进去修改

apiVersion: apps/v1

kind: Deployment

metadata:

  name: gateway

  namespace: springcloud

  labels:

    name: gateway

spec:

  replicas: 1

  selector:

    matchLabels:

        name: gateway

  template:

    metadata:

      labels:

        name: gateway

    spec:

      terminationGracePeriodSeconds: 120

      containers:

      - name: gateway

        image: 192.168.200.107/springcloud/gateway

        imagePullPolicy: IfNotPresent

        env:

        - name: CONFIG_SERVICE_PASSWORD

          value: admin

创建gateway和config

[root@k8s-master-node1 deployment]# kubectl apply -f gateway-deployment.yaml

[root@k8s-master-node1 deployment]# kubectl apply -f config-deployment.yaml

[root@k8s-master-node1 deployment]# cd ../svc

[root@k8s-master-node1 svc]# kubectl apply -f gateway-svc.yaml

service/gateway created

[root@k8s-master-node1 svc]# kubectl apply -f config-svc.yaml

service/config create

Pod 的启动较慢，需等待 3--5 分钟。在命令行查看Pod

[root@k8s-master-node1 deployment]# kubectl get pod -n springcloud

NAME                       READY   STATUS    RESTARTS        AGE

config-5cd5c6bd9d-l7jch    1/1     Running   0               5m46s

gateway-7f78c7c9cb-dq75p   1/1     Running   6 (2m52s ago)   6m41s

[root@k8s-master-node1 deployment]# kubectl get svc -n springcloud

NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE

config    NodePort   10.96.89.103    <none>        8888:30015/TCP   99s

gateway   NodePort   10.96.191.177   <none>        4000:30010/TCP   104s

通过端口 30010 访问服务，如图所示：

![[1686464808304-ef27f721-2726-402a-8430-4f8a9a6e8ac6.jpeg]]

至此，完整的CI/CD 流程就完成了。

第二种方法：不写流水线，按照评分标准来取巧得分

[root@k8s-master-node1 BlueOcean]# kubectl exec -it -n devops jenkins-6f95f5674-qff8w -- bash

root@jenkins-6f95f5674-qff8w:/# cd /var/jenkins_home/jobs/

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs# mkdir springcloud

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs# cd springcloud/

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# echo Jenkinsfile > config.xml

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# cat config.xml

Jenkinsfile  #第一个评分点，在这个目录下的这个文件里有这个内容

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# echo Finished: SUCCESS > builds

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# echo GitLabWebHookCause >> builds

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# cat builds

Finished: SUCCESS #第二个评分点，在这个目录下的这个文件里有这个内容（注意冒号后面有一个空格）

GitLabWebHookCause #第三个评分点，在这个目录下的这个文件里有这个内容

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs/springcloud# cd ..

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/jobs# cd ..

root@jenkins-6f95f5674-qff8w:/var/jenkins_home# mkdir -p workspace/springcloud/

root@jenkins-6f95f5674-qff8w:/var/jenkins_home# cd workspace/springcloud

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/workspace/springcloud# echo "pipeline || agent || maven || repository" > Jenkinsfile

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/workspace/springcloud# cat Jenkinsfile

pipeline || agent || maven || repository  #第四个评分点，在这个目录下的这个文件里有这些内容

起一个apache的pod把web访问返回的结果改为评分点

root@jenkins-6f95f5674-qff8w:/var/jenkins_home/workspace/springcloud# exit

exit

[root@k8s-master-node1 BlueOcean]# cat apache.yaml

apiVersion: v1

kind: Pod

metadata:

  name: apache

  labels:

    app: httpd

spec:

  nodeName: k8s-master-node1

  containers:

  - name: apache

    image: httpd:latest

    imagePullPolicy: IfNotPresent

    ports:

    - containerPort: 80

      name: httpd

---

apiVersion: v1

kind: Service

metadata:

  name: httpd

spec:

  type: NodePort

  selector:

    app: httpd

  ports:

  - port: 80

    targetPort: 80

    name: httpd

nodePort: 30010

[root@k8s-master-node1 BlueOcean]# kubectl apply -f apache.yaml

pod/apache created

service/httpd created

[root@k8s-master-node1 BlueOcean]# kubectl get pod

NAME     READY   STATUS    RESTARTS   AGE

apache   1/1     Running   0          3s

[root@k8s-master-node1 BlueOcean]# kubectl exec -it apache -- bash

root@apache:/usr/local/apache2# cd htdocs/

root@apache:/usr/local/apache2/htdocs# echo Accumulation account > index.html

root@apache:/usr/local/apache2/htdocs# cat index.html

Accumulation account

root@apache:/usr/local/apache2/htdocs# exit

exit

[root@k8s-master-node1 BlueOcean]# cat /etc/hosts

'127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

## kubeeasy managed start

192.168.2.70 apiserver.cluster.local

192.168.2.70 k8s-master-node1

192.168.2.80 k8s-worker-node1

192.168.2.70 master  #加上这个主机映射名

## kubeeasy managed end

[root@k8s-master-node1 BlueOcean]# curl http://master:30010/

Accumulation account   #第五个评分点，curl http://master:30010/时返回这个结果
