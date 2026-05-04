# 基于Kubernetes构建持续集成

基于Kubernetes构建持续集成相关技术笔记。
**1.1实战案例——基于Kubernetes构建持续集成**

**1.1.1 案例目标**

（1）了解Jenkins的离线安装步骤。

（2）掌握Gitlab的使用和管理。

（3）了解CICD的配置步骤和方法。

**1.1.2 案例分析**

**1.规划节点**

节点规划见表1-1-1。

表1-1-1节点规划

| **IP** | **主机名** | **节点** |
| :---: | :---: | :---: |
| 10.24.2.16 | master | master节点、Harbor仓库 |
| 10.24.2.17 | node | node节点 |

**2.基础准备**

登录OpenStack平台，使用提供的CentOS_7.5_x86_64_XD.qcow2镜像创建两台云主机，并使用提供的软件包部署好双节点Kubernetes集群。

**1.1.3 案例实施**

**1.安装Jenkins环境**

（1）基础环境准备

将提供的离线包CICD_Offline.tar上传至master节点/root目录下，解压文件：

[root@master ~]# tar -zvxf Jenkins.tar.gz

导入镜像并推送到Harbor仓库：一定要记得登录自己的Harbor仓库

[root@master Jenkins]# docker load -i images/jenkins_latest.tar

[root@master Jenkins]# docker tag jenkins/jenkins:latest 10.24.2.16/library/jenkins:latest

[root@master Jenkins]# docker push 10.24.2.16/library/jenkins:latest

[root@master Jenkins]# docker load -i images/gitlab-ce_latest.tar

[root@master Jenkins]# docker tag gitlab/gitlab-ce:latest 10.24.2.16/library/gitlab-ce:latest

[root@master Jenkins]# docker push 10.24.2.16/library/gitlab-ce:latest

[root@master Jenkins]# docker load -i images/java_8-jre.tar

[root@master Jenkins]# docker tag java:8-jre 10.24.2.16/library/java:8-jre

[root@master Jenkins]# docker push 10.24.2.16/library/java:8-jre

（2）安装Jenkins

编写Jenkins资源清单文件：

apiVersion: v1

kind: Service

metadata:

  name: jenkins

  labels:

    app: jenkins

spec:

  type: NodePort

  ports:

  - name: http

    port: 8080                      #服务端口

    targetPort: 8080

    nodePort: 30880                 #NodePort方式暴露 Jenkins 端口

  selector:

    app: jenkins

---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: jenkins

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

      nodeName: master

      serviceAccountName: jenkins-admin

      containers:

      - name: jenkins

        image: 10.24.2.16/library/jenkins:latest

        securityContext:

          runAsUser: 0                   #设置以ROOT用户运行容器

          privileged: true                  #拥有特权

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

部署Jenkins需要使用到一个拥有相关权限的serviceAccount，名称为jenkins，可以给jenkins赋予一些必要的权限，也可以直接绑定一个cluster-admin的集群角色权限，此处选择给予集群角色权限。

编写资源清单文件：

[root@master Jenkins]# cat jenkins-rbac.yaml

apiVersion: v1

kind: ServiceAccount

metadata:

  name: jenkins-admin

  labels:

    name: jenkins

---

kind: ClusterRoleBinding

apiVersion: rbac.authorization.k8s.io/v1

metadata:

  name: jenkins-admin

  labels:

    name: jenkins

subjects:

  - kind: ServiceAccount

    name: jenkins-admin

    namespace: default

roleRef:

  kind: ClusterRole

  name: cluster-admin

  apiGroup: rbac.authorization.k8s.io

创建资源：

[root@k8s-master-node1 jenkins-slave]# kubectl apply -f jenkins-deployment.yaml -f jenkins-rbac.yaml

service/jenkins created

deployment.apps/jenkins created

serviceaccount/jenkins-admin created

clusterrolebinding.rbac.authorization.k8s.io/jenkins-admin created

查看Pod：

[root@master Jenkins]# kubectl get pods

NAME                   READY   STATUS    RESTARTS   AGE

jenkins-b89d4c4d8-bc6tn   1/1       Running     0           50s

查看Jenkins Service端口：

[root@master Jenkins]# kubectl get svc

NAME     TYPE     CLUSTER-IP     EXTERNAL-IP     PORT(S)     AGE

jenkins  NodePort  10.96.53.202  <none>  8080:30880/TCP,50000:30850/TCP  113s

通过http://master_IP:30880访问Jenkins，如图所示：

![[1686464904125-0f9f2b6c-35d5-48a2-8541-678ed191fefd.png]]

获取Jenkins密码：

[root@master Jenkins]# kubectl exec deploy/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword

08f73c50281e4026bb2287abd7fa19f8

输入密码后点击“继续”，如图所示：

![[1686464904407-3c4ce017-8b46-49fe-b563-293efc2a9135.png]]

将离线插件包拷贝到Jenkins：

[root@master Jenkins]# kubectl cp plugins/ jenkins-b89d4c4d8-bc6tn:/var/jenkins_home

记得执行这条命令

echo 10.96.0.10 apiserver.cluster.local >> /etc/hosts

重启Jenkins：

[root@master Jenkins]# kubectl rollout restart deployment jenkins

deployment.apps/jenkins restarted

刷新Jenkins界面，选择“安装推荐的插件”，安装完成后进入用户创建页面，创建一个用户jenkins，密码000000，如图所示：

![[1686464904708-81027b28-a428-412e-a160-c6251a76d611.png]]

点击“保存并完成”，如图所示：

![[1686464904939-928f7b2f-3416-4b52-b19d-201487c51fbd.png]]

点击“保存并完成”，如图所示：

![[1686464905252-4f93c2cd-13ed-4c5a-94ea-75e674fa0d46.png]]

点击“开始使用Jenkins”并使用新创建的用户登录Jenkins，如图所示：

![[1686464905624-e39fa203-5432-4694-9cec-1aa88b5de89b.png]]

**2.部署Gitlab**

GitLab是利用Ruby on Rails一个开源的版本管理系统，实现一个自托管的Git项目仓库，可通过Web界面进行访问公开的或者私人项目。与Github类似，GitLab能够浏览源代码，管理缺陷和注释，可以管理团队对仓库的访问，它非常易于浏览提交过的版本并提供一个文件历史库，团队成员可以利用内置的简单聊天程序(Wall)进行交流。Gitlab还提供一个代码片段收集功能可以轻松实现代码复用，便于日后有需要的时候进行查找。

本项目Gitlab与Harbor共用一台服务器。

（1）部署GitLab

编写GitLab资源清单文件：

[root@master Jenkins]# cat gitlab-deployment.yaml

apiVersion: v1

kind: Service

metadata:

  name: gitlab

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

      - image: 10.24.2.16/library/gitlab-ce:latest

        name: gitlab

        imagePullPolicy: IfNotPresent

        env:

        - name: GITLAB_ROOT_PASSWORD

          value: admin123

        - name: GITLAB_ROOT_EMAIL

          value: guorui@example.com

        - name: GITLAB_PORT

          value: "80"

        ports:

        - containerPort: 443

          name: gitlab-443

        - containerPort: 80

          name: gitlab-80

启动Gitlab：

[root@master Jenkins]# kubectl apply -f gitlab-deployment.yaml

service/gitlab created

deployment.apps/gitlab created

查看Pod：

[root@master Jenkins]# kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE

gitlab-6778c45f9-xx5gs    1/1       Running     0            30s

jenkins-b89d4c4d8-bc6tn   1/1       Running     0            5m36s

查看GitLab Service：

[root@master Jenkins]# kubectl get svc

NAME  TYPE      CLUSTER-IP    EXTERNAL-IP       PORT(S)        AGE

gitlab    NodePort   10.107.127.252   <none>   443:30443/TCP,80:30888/TCP  51s

Gitlab启动较慢，可以通过docker logs查看启动状态。启动完成后，在web端访问Gitlab（http://master_IP:30888），如图所示：

![[1686464905906-22a53c13-9dfd-4c74-bc4d-4a5b680c2f77.png]]

登录Gitlab，如图所示：

![[1686464906181-2ffa7905-12c1-4eb7-b8b4-2b8f71b510e8.png]]

（2）创建项目

点击“New project”，如图所示：

![[1686464906452-207d3550-dbf9-4014-96b9-8fc6ecc6b20e.png]]

点击“Create blank project”创建项目springcloud，可见等级选择“Public”，如图所示：

![[1686464906734-526f563b-d142-43a9-bcab-49723418dda3.png]]

点击“Create project”，进入项目，如图所示：

![[1686464907014-97a22c3b-a016-47f8-b39a-fe7b4a7514d0.png]]

push源代码到gitlab的springcloud项目：

[root@master Jenkins]# yum install -y git

[root@master Jenkins]# cd springcloud/

[root@master springcloud]# git config --global user.name "administrator"

[root@master springcloud]# git config --global user.email "admin@example.com"

[root@master springcloud]# git remote remove origin

[root@master springcloud]# git remote add origin [http://10.24.2.16:30888/root/springcloud.git](http://10.24.2.16:30888/root/springcloud.git)

[root@master springcloud]# git add .

[root@master springcloud]# git commit -m "initial commit"

# On branch master

nothing to commit, working directory clean

[root@master springcloud]# git push -u origin master

Username for 'http://10.24.2.16:30888': root

Password for 'http://root@10.24.2.16:30888':     admin123 yaml文件定义的密码

Counting objects: 3192, done.

Delta compression using up to 8 threads.

Compressing objects: 100% (1428/1428), done.

Writing objects: 100% (3192/3192), 1.40 MiB | 1.70 MiB/s, done.

Total 3192 (delta 1233), reused 3010 (delta 1207)

remote: Resolving deltas: 100% (1233/1233), done.

remote:

remote: To create a merge request for master, visit:

remote:   http://gitlab-6778c45f9-xx5gs/root/springcloud/-/merge_requests/new?merge_request%5Bsource_branch%5D=master

remote:

To [http://10.24.2.16:30888/root/springcloud.git](http://10.24.2.16:30888/root/springcloud.git)

 * [new branch]      master -> master

Branch master set up to track remote branch master from origin.

刷新网页，springcloud项目master分支中的文件已经更新了，如图所示：

记得切换到master

![[1686464907314-26e33be3-6e7b-477a-a548-3c5a1bdf57ca.png]]

**3.配置Jenkins连接Gitlab**

（1）设置Outbound requests

登录Gitlab管理员界面（http://master_IP:30888/admin），如图所示：

![[1686464907596-dad7ddf6-2ecf-4cb5-8af2-623bfd651203.png]]

点击左侧导航栏的“Settings”→“Network”，设置“Outbound requests”，勾选“Allow requests to the local network from web hooks and services”，如图所示：

![[1686464907948-fdbc0490-0cac-483c-8f30-617b1c018ab7.png]]

配置完成后保存。

（2）创建Gitlab API Token

点击Gitlab用户头像图标，如图所示：

![[1686464908217-994be2ed-d228-42cf-bd72-b98ccca87d8c.png]]

点击“Preferences”，如图所示：

![[1686464908574-6315485c-f274-477a-885b-0bedc6bfc5c5.png]]

点击左侧导航栏的“Access Tokens”添加token，如图所示：

![[1686464908858-0e9ad6ba-1ad2-4c8a-83ee-c52c03fc6c69.png]]

这里一定要注意expiration date 的设置，尽量设远离当前的日期如果设置当天jenkins引用token可能报错

点击“Create personal access token”生成Token，如图所示：

![[1686464909113-38780004-daec-4e99-8770-9b425aa314cb.png]]

复制Token（4yN6xwNYafmYMg-gmyPd），后面配置Jenkins时会用到。

（3）设置Jenkins

登录Jenkins首页，点击“系统管理”→“系统配置”，配置Gitlab信息，取消勾选“Enable authentication for '/project' end-point”，输入“Connection name”和“Gitlab host URL”，然后点击“添加”→“Jenkins”添加认证信息，将Gitlab API Token填入，如图所示：

![[1686464909368-115af4cf-605f-4316-aa4c-1723cb0f17bf.png]]

点击“Test Connection”，如图所示：

![[1686464909746-8154945a-e641-421a-908c-eab97c0b47fc.png]]

**4.配置Jenkins连接maven**

（1）安装maven

由于Jenkins是采用docker in docker的方式启动的，所以需要在jenkins容器内安装maven：

[root@master springcloud]# cd ../

[root@master Jenkins]# kubectl cp apache-maven-3.6.3-bin.tar.gz jenkins-7d9bd56966-9kd72:/

[root@master Jenkins]# kubectl exec -it jenkins-7d9bd56966-9kd72 -- bash

root@jenkins-7d9bd56966-9kd72:/# tar -zxvf apache-maven-3.6.3-bin.tar.gz -C .

root@jenkins-7d9bd56966-9kd72:/# mv apache-maven-3.6.3/ /usr/local/maven

root@jenkins-7d9bd56966-9kd72:/# sed -i '$a\export M2_HOME=/usr/local/maven' /etc/profile

root@jenkins-7d9bd56966-9kd72:/# sed -i '$a\export PATH=\$PATH:\$M2_HOME/bin' /etc/profile

root@jenkins-7d9bd56966-9kd72:/# sed -i '$a\source /etc/profile' /root/.bashrc

退出容器重新进入：

[root@master Jenkins]# kubectl exec -it jenkins-7d9bd56966-9kd72 -- bash

root@jenkins-7d9bd56966-9kd72:/# mvn -v

Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)

Maven home: /usr/local/maven

Java version: 11.0.14.1, vendor: Eclipse Adoptium, runtime: /opt/java/openjdk

Default locale: en, platform encoding: UTF-8

OS name: "linux", version: "3.10.0-1160.el7.x86_64", arch: "amd64", family: "unix"

（2）连接maven

登录Jenkins首页，点击“系统管理”→“全局工具配置”，如图所示：

![[1686464910207-566a730f-6553-4541-99ac-6f10e6a8cb27.png]]

点击“新增Maven”，如图所示。取消勾选“自动安装”，填入maven名称和安装路径，配置完成后点击“应用”。

![[1686464910468-ee416274-298c-49f1-86fa-a7b1c466b049.png]]

**5. 配置CI/CD**

（1）新建任务

登录Jenkins首页，点击左侧导航栏“新建任务”，如图所示，选择构建一个流水线。

![[1686464911013-e7648592-573b-4c18-8f58-c27a93d9e418.png]]

点击“确定”，配置构建触发器，如图所示：

![[1686464911327-5cfa7202-fd83-45b4-a26f-dc447a20664f.png]]

记录下GitLab webhook URL的地址（[http://10.24.2.16:30880/project/springcloud](http://10.24.2.16:30880/project/springcloud)），后期配置webhook需要使用。 [http://192.168.100.35:30880/project/springcloud](http://192.168.100.35:30880/project/springcloud)

配置流水线，如图所示：

![[1686464911629-96cca5d6-bc11-4e5d-8d1e-2f24a86579fd.png]]

点击“流水线语法”，如图所示，示例步骤选择“git：Git”，将springcloud项目地址填入仓库URL。

![[1686464911890-19b45888-cfda-4632-b401-98fcce43be8b.png]]

点击“添加”→“jenkins”添加凭据，如图所示。类型选择“Username with password”，用户名和密码为Gitlab仓库的用户名和密码。

![[1686464912157-d084b4eb-57fd-46f2-be11-f246fd58de1a.png]]

添加凭据后选择凭据，如图所示：

![[1686464912443-8684789b-d316-48f2-bf58-657d723fde13.png]]

点击“生成流水线脚本”，如图所示：

git credentialsId: '2ebe2381-fa16-47ce-b1da-8b5c361266d3', url: 'http://192.168.100.35:30888/root/springcloud.git'

![[1686464912666-023d5aae-6e96-4291-b46b-dd06479d7db6.png]]

记录生成的值，并将其写入流水线脚本中，完整的流水线脚本如下：

node{

    stage('git clone'){

        //check CODE

        git credentialsId: '50e9f79a-7bea-47ab-a705-1110843bc3b0', url: 'http://10.24.2.16:30888/root/springcloud.git'

    }

    stage('maven build'){

        sh '''/usr/local/maven/bin/mvn package -DskipTests -f /var/jenkins_home/workspace/springcloud'''

    }

    stage('image build'){

        sh '''

              echo $BUILD_ID

              docker build -t 10.24.2.16/springcloud/gateway:$BUILD_ID -f /var/jenkins_home/workspace/springcloud/gateway/Dockerfile  /var/jenkins_home/workspace/springcloud/gateway

              docker build -t 10.24.2.16/springcloud/config:$BUILD_ID -f /var/jenkins_home/workspace/springcloud/config/Dockerfile  /var/jenkins_home/workspace/springcloud/config'''

    }

    stage('test'){

        sh '''docker run -itd --name gateway 10.24.2.16/springcloud/gateway:$BUILD_ID

        docker ps -a|grep springcloud|grep Up

        if [ $? -eq 0 ];then

            echo "Success!"

            docker rm -f gateway

        else

            docker rm -f gateway

            exit 1

            fi

        '''

    }

    stage('upload registry'){

        sh '''docker login 10.24.2.16 -u=admin -p=Harbor12345

            docker push 10.24.2.16/springcloud/gateway:$BUILD_ID

            docker push 10.24.2.16/springcloud/config:$BUILD_ID'''

    }

    stage('deploy Rancher'){

        //执行部署脚本

       sh 'sed -i "s/sqshq\\/piggymetrics-gateway/10.24.2.16\\/springcloud\\/gateway:$BUILD_ID/g" /var/jenkins_home/workspace/springcloud/yaml/deployment/gateway-deployment.yaml'

       sh 'sed -i "s/sqshq\\/piggymetrics-config/10.24.2.16\\/springcloud\\/config:$BUILD_ID/g" /var/jenkins_home/workspace/springcloud/yaml/deployment/config-deployment.yaml'

       sh 'kubectl create ns springcloud'

       sh 'kubectl apply -f /var/jenkins_home/workspace/springcloud/yaml/deployment/gateway-deployment.yaml --kubeconfig=/root/.kube/config'

       sh 'kubectl apply -f /var/jenkins_home/workspace/springcloud/yaml/deployment/config-deployment.yaml --kubeconfig=/root/.kube/config'

       sh 'kubectl apply -f /var/jenkins_home/workspace/springcloud/yaml/svc/gateway-svc.yaml --kubeconfig=/root/.kube/config'

       sh 'kubectl apply -f /var/jenkins_home/workspace/springcloud/yaml/svc/config-svc.yaml --kubeconfig=/root/.kube/config'

    }

}

脚本中所有IP均为Harbor仓库的地址。

在网页写入完整的流水线脚本，如图所示，完成后点击“应用”。

![[1686464912947-8c998399-70c1-454b-ad2d-bded068a6182.png]]

（2）开启Jenkins匿名访问

登录Jenkins首页，点击“系统管理”→“全局安全配置”，勾选“任何用户可以做任何事(没有任何限制)”，如图所示。

![[1686464913187-18d2f6bc-a5cd-4f50-b234-1e1471520205.png]]

（3）创建仓库项目

登录Harbor，新建项目springcloud，访问级别设置为公开，创建完成后如图所示：

![[1686464913397-61172224-315e-4862-8540-2ee87fb9c7e6.png]]

进入项目查看镜像列表，如图所示，此时为空，无任何镜像：

![[1686464913661-6d8635da-9495-4f09-bac8-5e22b01c060a.png]]

**6. 触发CI/CD**

（1）触发构建

登录Gitlab，进入springcloud项目，点击左侧导航栏“Settings”→“Webhooks”，将前面记录的GitLab webhook URL地址填入URL处，禁用SSL认证，如图所示。

![[1686464913884-769cf6ea-c4a7-44a3-90b9-f5db5c1942b2.png]]

一定要记得取消勾选ssl认证

点击“Add webhook”添加webhook，完成后如图所示：

![[1686464914159-b6f1e631-e8fe-4fb6-bdda-f47bbeae7831.png]]

点击“Test”→“Push events”进行测试，如图所示：

![[1686464914385-97a3b6d5-00a7-4c9d-91f1-7e00798578d0.png]]

结果返回HTTP 200则表明Webhook配置成功。

（2）Jenkins查看

登录Jenkins，可以看到springcloud项目已经开始构建，如图所示：

![[1686464914670-a54254d1-da03-4bfe-bdc4-9532ce02ab55.png]]

点击项目名称查看流水线阶段视图，如图所示：

![[1686464914930-b56f66c7-4e75-43cb-9924-da8b691b2b92.png]]

可以看到，项目构建失败，这是因为未配置Maven Repository：

[root@master Jenkins]# kubectl cp repository/ jenkins-7d9bd56966-9kd72:/root/.m2/

在浏览器端输入“http://master_IP:30880/restart”重启Jenkins，重启完成后点击右侧导航栏的“立即构建”，如图所示：

+ kubectl create ns springcloud

Unable to connect to the server: dial tcp: lookup apiserver.cluster.local on 10.96.0.10:53: no such host

#已解决echo 10.96.0.10 apiserver.cluster.local >> /etc/hosts  Jenkins写入域名解析就成功了

![[1686464915308-fe164c5b-5831-48cc-8582-0d0a92b34a6e.png]]

切换到“Console Output”界面可查看控制台输出，此处会显示构建的详细进程，如图所示：

![[1686464915591-ea112ead-1cfe-4f46-8966-8c81d66e884b.png]]

构建完成后控制台输出如图所示：

![[1686464915863-b3ac5697-09aa-4a18-b149-fa4adb057ca7.png]]

返回项目查看流水线阶段视图，如图所示：

![[1686464916184-1b5f0dc9-b330-47e8-9bc5-cc26a08bf093.png]]

（3）Harbor查看

进入Harbor仓库springcloud项目查看镜像列表，可以看到已自动上传了一个gateway镜像，如图所示：

![[1686464916507-2f88e831-9e5e-4c12-b5f6-f1431f8a8370.png]]

（4）Kubernetes查看

Pod的启动较慢，需等待3--5分钟。在命令行查看Pod：

[root@master ~]# kubectl -n springcloud get pods

NAME                   READY   STATUS    RESTARTS   AGE

config-6c988c4dc5-2522c    1/1       Running     0           21m

gateway-6545fc58c5-d6rgn   1/1       Running     0           21m

查看service：

[root@master ~]# kubectl -n springcloud get service

NAME   TYPE      CLUSTER-IP   EXTERNAL-IP   PORT(S)       AGE

config    NodePort   10.101.42.47    <none>        8888:30015/TCP   22m

gateway   NodePort   10.100.62.39    <none>        4000:30010/TCP   22m

通过端口30010访问服务，如图所示：

![[1686464916806-7300ef05-0d40-4f47-85b0-f15c2c88a390.png]]

至此，完整的CI/CD流程就完成了。

![[1686464917099-f926f2c0-be79-44df-944c-2dc2558e451e.png]]
