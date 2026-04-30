_**【赛程名称】云计算赛项第二场-容器云**_

_**说明：完成本任务需要两台安装了CentOS7.5操作系统的云主机：master和node。Chinaskill_Cloud_PaaS.iso镜像包中有本次容器云部署所需的所有文件，运维所需的文件见附件。**_

_**某公司技术部产品开发上线周期长，客户的需求经常得不到及时响应。引入CICD (Continuous Integration持续集成、Continuous Delivery持续交付) 成了公司的当务之急，研发团队决定搭建基于Kubernetes 的CICD环境，希望基于这个平台来实现DevOps的部分流程，来减轻开发、部署、运维的负担。**_

_**为了能够让公司开发的web应用系统产品能够基于服务器的性能、可靠性、高可用性与方便维护，研发部决定使用微服务架构，实现基于Kubernetes的容器化部署。**_

_**节点规划如表1所示。**_

_**表1容器云平台节点规划**_

| _**节点角色**_ | _**主机名**_ | _**VCPUS**_ | _**内存**_ | _**硬盘**_ |
| :---: | :---: | :---: | :---: | :---: |
| _**Master、Harbor、CICD**_ | _**master**_ | _**8**_ | _**12G**_ | _**100G**_ |
| _**Worker Node**_ | _**node**_ | _**8**_ | _**12G**_ | _**100G**_ |

_**【任务1】Docker CE及私有仓库安装任务[5.0分]**_

_**【适用平台】私有云**_

_**【题目1】安装Docker CE和Docker Compose[1.0分]**_

_**登录提供的OpenStack平台，使用kubernetes镜像创建两台云主机，需要用到的软件包已解压到云主机的/opt目录下。在两台云主机上分别安装DockerCE和docker-compose服务。**_

_**完成后提交master节点的用户名、密码和IP到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**docker info**_

_**【判分标准】[0.5分]**_

_**Server Version: 20.10.8**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**docker-compose version**_

_**【判分标准】[0.5分]**_

_**Docker Compose version v2.2.1**_

_**【判分结束】**_

_**【题目2】集群部署[1.0分]**_

_**在master节点和node节点完成Kubernetes集群的安装。**_

_**完成后提交master节点的用户名、密码和IP到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**kubectl get cs**_

_**【判分标准】[0.5分]**_

_**controller-manager   Healthy   ok || scheduler            Healthy   ok**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**kubectl get node**_

_**【判分标准】[0.5分]**_

_**Ready**_

_**【判分结束】**_

_**【题目3】容器编排[3.0分]**_

_**在master节点上编写/root/rabbitmq-cluster/docker-compose.yml文件编排部署RabbitMQ HA集群，该集群包含三个节点，三个节点的服务名称和容器名称分别为rabbitmq-node-1、rabbitmq-node-2和rabbitmq-node-3，启用RabbitMQ Management，用户名为admin，密码为Admin@123。**_

_**完成后编排部署RabbitMQ集群，并提交master节点的用户名、密码和IP到答题框。（需要用到的Docker镜像包路径**_[_**http://10.24.1.46/Rabbitmq-cluster.tar**_](http://10.24.1.46/Rabbitmq-cluster.tar)_**）**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**curl -L http://$(hostname -i):15672**_

_**【判分标准】[0.5分]**_

_**RabbitMQ Management**_

_**【判分结束】**_

_**【检测命令2】[1.5分]**_

_**docker exec  rabbitmq-node-1 bash -c "rabbitmqctl cluster_status"**_

_**【判分标准】[1.5分]**_

_**Node: rabbit@rabbitmq-node-1||Node: rabbit@rabbitmq-node-2||Node: rabbit@rabbitmq-node-3**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**docker exec  rabbitmq-node-2 bash -c " rabbitmqctl authenticate_user admin Admin@123"**_

_**【判分标准】[1.0分]**_

_**Success**_

_**【判分结束】**_

_**【任务2】基于Docker容器的web应用系统部署[13.0分]**_

_**【适用平台】私有云**_

_**云梦公司开发了一套基于Django架构的博客系统，并实现全容器化部署。**_

_**【题目1】容器化Memcached服务[2.0分]**_

_**在master节点/root/DjangoBlog目录下编写Dockerfile-memcached文件构建blog-memcached:v1.0镜像，具体要求如下：（需要用到的软件包：**_[_**http://10.24.1.46/Django.tar.gz**_](http://10.24.1.46/Django.tar.gz)_**）**_

_**（1）基础镜像：centos:7.9.2009；**_

_**（2）完成memcached服务的安装；**_

_**（3）声明端口：11211；**_

_**（4）设置服务开机自启。**_

_**完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cd /root/DjangoBlog && docker build -t blog-memcached:v1.0 -f Dockerfile-memcached .**_

_**【判分标准】[0.5分]**_

_**memcached || Successfully built**_

_**【判分结束】**_

_**【检测命令2】[1.5分]**_

_**【判分标准】[1.5分]**_

_**memcached**_

_**【判分结束】**_

_**【题目2】容器化Mariadb服务[2.0分]**_

_**在master节点/root/DjangoBlog目录下编写Dockerfile-mariadb文件构建blog-mysql:v1.0镜像，具体要求如下：（需要用到的软件包：**_[_**http://10.24.1.46/Django.tar.gz**_](http://10.24.1.46/Django.tar.gz)_**）**_

_**（1）基础镜像：centos:7.9.2009；**_

_**（2）安装MariaDB服务并设置root用户的密码为root；**_

_**（3）创建数据库djangoblog并将sqlfile.sql导入该数据库；**_

_**（4）声明端口：3306；**_

_**（5）设置服务开机自启。**_

_**完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cd /root/DjangoBlog/ && docker build -t blog-mysql:v1.0 -f Dockerfile-mariadb .**_

_**【判分标准】[0.5分]**_

_**sqlfile.sql || Successfully built**_

_**【判分结束】**_

_**【检测命令2】[1.5分]**_

_**【判分标准】[1.5分]**_

_**root@localhost || 10.3.32-MariaDB || accounts_bloguser || django_admin_log || servermanager_commands**_

_**【判分结束】**_

_**【题目3】容器化前端服务[1.5分]**_

_**在master节点/root/DjangoBlog目录下编写Dockerfile-nginx文件构建blog-nginx:v1.0镜像，具体要求如下：（需要用到的软件包：**_[_**http://10.24.1.46/Django.tar.gz**_](http://10.24.1.46/Django.tar.gz)_**）**_

_**（1）基础镜像：centos:7.9.2009；**_

_**（2）安装nginx服务；**_

_**（3）使用提供的nginx.conf作为默认的配置文件；**_

_**（3）声明端口：80；**_

_**（4）设置服务开机自启。**_

_**完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**cd /root/DjangoBlog/ && docker build -t blog-nginx:v1.0 -f Dockerfile-nginx .**_

_**【判分标准】[0.5分]**_

_**nginx.conf || Successfully built**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**docker run -d --name nginx-exam-jiance blog-nginx:v1.0 && docker logs nginx-exam-jiance && docker rm -f nginx-exam-jiance**_

_**【判分标准】[1.0分]**_

_**djangoblog**_

_**【判分结束】**_

_**【题目4】容器化Blog服务[2.5分]**_

_**在master节点/root/DjangoBlog目录下编写Dockerfile-blog文件构建blog-service:v1.0镜像，具体要求如下：（需要用到的软件包：**_[_**http://10.24.1.46/Django.tar.gz**_](http://10.24.1.46/Django.tar.gz)_**）**_

_**（1）基础镜像：centos:7.9.2009；**_

_**（2）安装Python3.6环境；**_

_**（3）使用pip3工具离线安装requirements.txt中的软件包；**_

_**（4）安装DjangoBlog服务；**_

_**（5）声明端口：8000；**_

_**（6）设置DjangoBlog服务开机自启。**_

_**完成后构建镜像，并提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**cd /root/DjangoBlog/ && docker build -t blog-service:v1.0 -f Dockerfile-blog .**_

_**【判分标准】[1.5分]**_

_**requirements.txt || pip3 install || --no-index**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**docker run -d --name blog-exam-jiance blog-service:v1.0 && docker logs blog-exam-jiance && docker rm -f blog-exam-jiance**_

_**【判分标准】[1.0分]**_

_**Starting djangoblog as root**_

_**【判分结束】**_

_**【题目5】编排部署博客系统[5.0分]**_

_**在master节点/root/DjangoBlog目录下编写docker-compose.yaml文件，具体要求如下：**_

_**（1）容器1名称：blog-memcached；镜像：blog-memcached:v1.0；端口映射：11211:11211；**_

_**（2）容器2名称：blog-mysql；镜像：blog-mysql:v1.0；端口映射：3306:3306；**_

_**（3）容器3名称：blog-nginx；镜像：blog-nginx:v1.0；端口映射：80:8888；**_

_**（4）容器4名称：blog-service；镜像：blog-service:v1.0；端口映射：8000:8000。**_

_**完成后编排部署该博客系统，并提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**docker-compose -f /root/DjangoBlog/docker-compose.yaml config**_

_**【判分标准】[0.5分]**_

_**links || container_name: blog-service**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**curl -L http://$(hostname -i):8888**_

_**【判分标准】[1.0分]**_

_**djangoblog**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**docker exec blog-service python3 --version**_

_**【判分标准】[1.0分]**_

_**Python 3.6.5**_

_**【判分结束】**_

_**【检测命令4】[1.0分]**_

_**docker exec blog-service pip3 list**_

_**【判分标准】[1.0分]**_

_**Django             3.2.10 || mysqlclient        2.1.0**_

_**【判分结束】**_

_**【检测命令5】[1.0分]**_

_**docker exec blog-nginx cat /etc/nginx/nginx.conf**_

_**【判分标准】[1.0分]**_

_**/code/djangoblog/collectedstatic/**_

_**【判分结束】**_

_**【检测命令6】[0.5分]**_

_**docker exec blog-service printenv**_

_**【判分标准】[0.5分]**_

_**DJANGO_MYSQL_DATABASE=djangoblog || DJANGO_MYSQL_PASSWORD=root**_

_**【判分结束】**_

_**【任务3】基于Kubernetes构建持续集成[15.0分]**_

_**【适用平台】私有云**_

_**该公司决定采用Kubernetes + GitLab CI来构建CICD环境，以缩短新功能开发上线周期，及时满足客户的需求，实现DevOps的部分流程，来减轻部署运维的负担，实现可视化容器生命周期管理、应用发布和版本迭代更新，请完成GitLab CI + Kubernetes的CICD环境部署（构建持续集成所需要的所有软件包在软件包CICD-Runner.tar.gz中）。CICD应用系统架构如下：**_

![[1685588982592-489ca177-c1f8-409b-ac1e-27b2e6b87300.png]]

_**【题目1】安装GitLab环境[3.0分]**_

_**在Kubernetes集群中新建命名空间kube-ops，将GitLab部署到该命名空间下，Deployment和Service名称均为gitlab，以NodePort方式将80端口对外暴露为30880，并设置GitLab服务root用户的密码为admin@123。**_

_**完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径**_[_**http://10.24.1.46/CICD-Runner.tar.gz**_](http://10.24.1.46/CICD-Runner.tar.gz)_**）**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.5分]**_

_**kubectl -n kube-ops exec deploy/gitlab -- gitlab-ctl service-list**_

_**【判分标准】[1.5分]**_

_**gitlab-monitor* || gitlab-workhorse* || postgresql* || redis***_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**curl -L http://$(hostname -i):30880/admin**_

_**【判分标准】[0.5分]**_

_**new_user_name**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**kubectl -n kube-ops exec deploy/gitlab -- printenv**_

_**【判分标准】[1.0分]**_

_**GITLAB_ROOT_PASSWORD=admin@123**_

_**【判分结束】**_

_**【题目2】部署GitLab Runner[4.0分]**_

_**在Kubernetes集群kube-ops命名空间下使用StatefulSet资源对象完成GitLab Runner的部署，StatefulSet名称为gitlab-ci-runner，副本数为2，并完成GitLab Runner在GitLab中的注册。**_

_**完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径**_[_**http://10.24.1.46/CICD-Runner.tar.gz**_](http://10.24.1.46/CICD-Runner.tar.gz)_**）**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**kubectl -n kube-ops get cm -o jsonpath={.items[*].data}**_

_**【判分标准】[1.0分]**_

_**30880**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**kubectl -n kube-ops exec statefulset.apps/gitlab-ci-runner -- gitlab-runner list**_

_**【判分标准】[1.0分]**_

_**gitlab-ci-runner-0 || 30880**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**kubectl -n kube-ops exec statefulset.apps/gitlab-ci-runner -- cat /home/gitlab-runner/.gitlab-runner/config.toml**_

_**【判分标准】[1.0分]**_

_**30880**_

_**【判分结束】**_

_**【检测命令4】[1.0分]**_

_**kubectl -n kube-ops logs gitlab-ci-runner-0**_

_**【判分标准】[1.0分]**_

_**Runner registered successfully**_

_**【判分结束】**_

_**【题目3】配置GitLab[2.0分]**_

_**在GitLab中新建公开项目springcloud，然后将Kubernetes集群添加到GitLab中，项目命名空间选择gitlab。**_

_**完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径**_[_**http://10.24.1.46/CICD-Runner.tar.gz**_](http://10.24.1.46/CICD-Runner.tar.gz)_**）**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[2.0分]**_

_**kubectl -n kube-ops exec deploy/gitlab -- cat var/log/gitlab/gitlab-rails/production.log**_

_**【判分标准】[2.0分]**_

_**6443 || BEGIN CERTIFICATE || "namespace"=>"gitlab" || "project_id"=>"springcloud"**_

_**【判分结束】**_

_**【题目4】构建CICD[6.0分]**_

_**将提供的代码推送到GitLab项目springcloud中，编写流水线脚本.gitlab-ci.yml触发自动构建，要求完成构建代码、构建镜像springcloud:master、推送镜像到library项目并发布服务到gitlab命名空间下。**_

_**完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包路径**_[_**http://10.24.1.46/CICD-Runner.tar.gz**_](http://10.24.1.46/CICD-Runner.tar.gz)_**）**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[2.5分]**_

_**curl -L http://$(hostname -i):30800**_

_**【判分标准】[2.5分]**_

_**Hello Golang In Gitlab CI || go1.10.3**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**kubectl -n kube-system get cm coredns -o json**_

_**【判分标准】[1.0分]**_

_**gitlab || fallthrough**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**docker pull $(hostname -i)/library/springcloud:master && docker inspect $(hostname -i)/library/springcloud:master**_

_**【判分标准】[1.0分]**_

_**/bin/app**_

_**【判分结束】**_

_**【检测命令4】[0.5分]**_

_**kubectl -n gitlab get deploy**_

_**【判分标准】[0.5分]**_

_**gitlab-k8s-demo-dev||2/2**_

_**【判分结束】**_

_**【检测命令5】[1.0分]**_

_**【判分标准】[1.0分]**_

_**yidaoyun/golang:v1.0 || stages || make build || yidaoyun/docker-dind:v1.0**_

_**【判分结束】**_

_**【任务4】Kubernetes容器云平台部署与运维[11.0分]**_

_**【适用平台】私有云**_

_**【题目1】Pod时间同步[1.0分]**_

_**容器默认的时区采用的是UTC时区，而宿主机采用的是CST时区。使用nginx:latest镜像在default命名空间下创建一个名为exam的Pod，要求Pod时区与宿主机时区同步。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**kubectl get pods exam -o yaml**_

_**【判分标准】[1.0分]**_

_**path: /etc/localtime**_

_**【判分结束】**_

_**【题目2】设置环境变量[1.0分]**_

_**在default命名空间下使用nginx:latest镜像启动一个名为env-demo的Pod，为该Pod的容器配置环境变量DEMO_GREETING，其值为“Hello from the environment”。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**kubectl exec env-demo -- printenv**_

_**【判分标准】[1.0分]**_

_**DEMO_GREETING=Hello from the environment**_

_**【判分结束】**_

_**【题目3】Pod服务质量管理[0.5分]**_

_**在default命名空间下使用nginx:latest镜像创建一个QoS类为Guaranteed的Pod，名称为qos-demo。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**kubectl get pod qos-demo -o yaml**_

_**【判分标准】[0.5分]**_

_**qosClass: Guaranteed**_

_**【判分结束】**_

_**【题目4】生命周期管理[1.0分]**_

_**在default命名空间下使用nginx:latest镜像创建一个名为lifecycle-demo的Pod，要求容器创建成功后执行命令“echo Hello from the postStart handler > /usr/share/message”，容器终止前执行命令“nginx -s quit; while killall -0 nginx; do sleep 1; done”。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.5分]**_

_**kubectl exec -it lifecycle-demo -- cat /usr/share/message**_

_**【判分标准】[0.5分]**_

_**Hello from the postStart handler**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**kubectl get pods lifecycle-demo -o yaml**_

_**【判分标准】[0.5分]**_

_**preStop || while killall -0 nginx**_

_**【判分结束】**_

_**【题目5】Opaque Secret[1.0分]**_

_**在default命名空间下创建一个名为exam的Opaque Secret，要求data字段存储两个字符串：username和password，username的值为admin（需用base64进行转码），password的值为123456（需用base64进行转码）。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**kubectl get secret exam -o jsonpath='{.data}'**_

_**【判分标准】[1.0分]**_

_**"password":"MTIzNDU2"||"username":"YWRtaW4="**_

_**【判分结束】**_

_**【题目6】服务部署[2.0分]**_

_**在Kubernetes集群default命名空间下完成ownCloud云盘系统的部署。启动一个名为owncloud的Deployment，包含一个Pod，Pod内包含两个容器owncloud（镜像：owncloud:latest）和mysql（mysql:5.6）。为该Deployment创建一个名为owncloud-svc的Service，以NodePort方式将容器的80端口对外暴露为30003。**_

_**完成后提交master节点的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**curl -L http://$(hostname -i):30003**_

_**【判分标准】[1.0分]**_

_**ownCloud**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**kubectl get deployment owncloud -o jsonpath={.spec.template.spec.containers[*].name}**_

_**【判分标准】[0.5分]**_

_**owncloud || mysql**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**kubectl get all**_

_**【判分标准】[0.5分]**_

_**deployment.apps/owncloud   1/1     1            1 || service/owncloud-svc   NodePort**_

_**【判分结束】**_

_**【题目7】节点选择器[1.0分]**_

_**为node节点打上标签“disktype=ssd”，在default命名空间下使用nginx:latest镜像创建一个名为nginx的Pod，将其调度到有“disktype=ssd”标签的节点上。**_

_**完成后提交master节点的IP、用户名和密码到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.4分]**_

_**kubectl get node --show-labels**_

_**【判分标准】[0.4分]**_

_**disktype=ssd**_

_**【判分结束】**_

_**【检测命令2】[0.6分]**_

_**kubectl get pods nginx -o yaml**_

_**【判分标准】[0.6分]**_

_**disktype: ssd**_

_**【判分结束】**_

_**【题目8】服务网格-安装Istio[1.0分]**_

_**使用提供的软件包Istio-Offline.tar.gz在Kubernetes集群istio-system命名空间下完成Istio服务网格demo配置档环境的安装，并完成可视化插件Grafana、Prometheus、Kiali及Jaeger的安装。（需要用到的软件包：**_[_**http://10.24.1.46/Istio-Offline.tar.gz**_](http://10.24.1.46/Istio-Offline.tar.gz)_**）**_

_**完成后提交master节点的用户名、密码及IP到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[0.8分]**_

_**kubectl -n istio-system get all**_

_**【判分标准】[0.8分]**_

_**deployment.apps/grafana                1/1 || service/prometheus             NodePort**_

_**【判分结束】**_

_**【检测命令2】[0.2分]**_

_**istioctl version**_

_**【判分标准】[0.2分]**_

_**client version: 1.12.0**_

_**【判分结束】**_

_**【题目9】服务网格-服务发布[2.5分]**_

_**为Kubernetes集群default命名空间启用sidecar自动注入，将bookinfo应用的v1版本部署到default命名空间下，部署文件在/root/ServiceMesh/bookinfo/目录下，然后为bookinfo应用创建网关bookinfo-gateway，让所有HTTP流量通过80端口流入网格，并启用Istio，将流量导向bookinfo应用。（需要用到的软件包：**_[_**http://10.24.1.82/competition/Bookinfo.tar.gz**_](http://10.24.1.82/competition/Bookinfo.tar.gz)_**） 完成后提交master节点的用户名、密码及IP到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**kubectl get pods**_

_**【判分标准】[1.0分]**_

_**details||productpage||ratings||reviews**_

_**【判分结束】**_

_**【检测命令2】[0.5分]**_

_**kubectl get svc -n istio-system**_

_**【判分标准】[0.5分]**_

_**istio-egressgateway||istio-egressgateway**_

_**【判分结束】**_

_**【检测命令3】[0.5分]**_

_**kubectl get gateway**_

_**【判分标准】[0.5分]**_

_**bookinfo-gateway**_

_**【判分结束】**_

_**【检测命令4】[0.5分]**_

_**kubectl get destinationrule**_

_**【判分标准】[0.5分]**_

_**details||productpage||ratings||reviews**_

_**【判分结束】**_

_**【任务5】Kubernetes运维开发任务[6.0分]**_

_**【适用平台】私有云**_

_**【题目1】Kubernetes Python运维脚本开发：使用SDK方式管理deployment服务[3.0分]**_

_**在提供的OpenStack私有云平台上，使用“k8s? HYPERLINK "http://10.24.1.60/dashboard/ngdetails/OS::Glance::Image/ff452418-8afe-4e1a-b0ff-f99bc339c509" ?-python-dev?”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/1DaoYun@2022”。使用Kubernetes python SDK的“kubernetes”Python库，在/root目录下，创建sdk_manager_deployment.py文件，要求编写python代码，代码实现以下任务：**_

_**（1）首先使用nginx-deployment.yaml文件创建deployment资源。**_

_**（2）创建完成后，查询该服务的信息，查询的body部分通过控制台输出，并以json格式的文件输出到当前目录下的deployment_sdk_dev.json文件中。**_

_**编写完成后，提交该云主机的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat /root/sdk_manager_deployment.py**_

_**【判分标准】[1.0分]**_

_**kubernetes || with open || yaml. || create_namespaced_deployment**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**python3 /root/sdk_manager_deployment.py**_

_**【判分标准】[1.0分]**_

_**{'api_version' ||'apps/v1'|| 'kind' || 'metadata': ||'containers'**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**cat /root/deployment_sdk_dev.json**_

_**【判分标准】[1.0分]**_

_**api_version || kind || metadata || host_ip**_

_**【判分结束】**_

_**【题目2】Kubernetes Python运维脚本开发：使用Restful API方式管理service服务[3.0分]**_

_**在提供的OpenStack私有云平台上，使用 “k8s? HYPERLINK "http://10.24.1.60/dashboard/ngdetails/OS::Glance::Image/ff452418-8afe-4e1a-b0ff-f99bc339c509" ?-python-dev?”镜像创建1台云主机，云主机类型使用4vCPU/12G内存/100G硬盘。该主机中已经默认安装了所需的开发环境，登录默认账号密码为“root/1DaoYun@2022”。使用python request库和Kubernetes Restful APIs，在/root目录下，创建api_manager_service.py文件，要求编写python代码，代码实现以下任务：**_

_**（1）首先查询查询服务service，如果service名称“nginx-svc”已经存在，先删除。**_

_**（2）如果不存在“nginx-svc”，使用service.yaml文件创建服务。**_

_**（3）创建完成后，查询该服务的信息，查询的body部分以json格式的文件输出到当前目录下的service_api_dev.json文件中。**_

_**（4）然后使用service_update.yaml更新服务端口。**_

_**（5）完成更新后，查询该服务的信息，信息通过控制台输出，并通过json格式追加到service_api_dev.json文件后。**_

_**编写完成后，提交该云主机的用户名、密码和IP地址到答题框。**_

_**【检测类型】命令行检测**_

_**【登录管理器】账户密码**_

_**【检测命令1】[1.0分]**_

_**cat api_manager_service.py**_

_**【判分标准】[1.0分]**_

_**import requests || yaml || json || bearer**_

_**【判分结束】**_

_**【检测命令2】[1.0分]**_

_**python3 api_manager_service.py**_

_**【判分标准】[1.0分]**_

_**name || resourceVersion || targetPort || port**_

_**【判分结束】**_

_**【检测命令3】[1.0分]**_

_**cat service_api_dev.json**_

_**【判分标准】[1.0分]**_

_**name || resourceVersion || targetPort || port**_

_**【判分结束】**_
