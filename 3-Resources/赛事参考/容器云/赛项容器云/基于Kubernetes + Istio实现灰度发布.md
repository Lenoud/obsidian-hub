# 基于Kubernetes + Istio实现灰度发布

### 案例分析[基于Kubernetes Istio实现灰度发布.mp4](https://fdfs.douxuedu.com/group1/M00/00/4B/wKggBmIq6JOEOxYAAAAAACCGsUw029.mp4)

#### 1. 规划节点

节点规划，见表1。

表1 节点规划

| **IP** | **主机名** | **节点** |
| --- | --- | --- |
| 10.24.2.5 | k8s-master-node1 | master节点、仓库节点 |

#### 2. 基础准备

已基于软件包chinaskills_cloud_paas_v2.0.iso部署完Kubernetes集群，并将提供的软件包ServiceMesh.tar.gz上传至master节点/root目录下。

### 案例实施

#### 1. 基础环境准备

##### （1）导入软件包

下载并解压软件包：

```text
[root@master ~]# wget http://mirrors.douxuedu.com/competition/ServiceMesh.tar.gz
[root@master ~]# tar -xf ServiceMesh.tar.gz
```

导入所有镜像：

```text
[root@master ~]# docker load -i ServiceMesh/images/image.tar
```

##### （2）启动Kubernetes集群

初始化Kubernetes集群：

```text
[root@master ~]# init-cluster
```

查看集群状态：

```text
[root@master ~]# kubectl cluster-info
Kubernetes control plane is running at https://apiserver.cluster.local:6443
CoreDNS is running at https://apiserver.cluster.local:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

#### 2. 部署Bookinfo应用

##### （1）部署应用程序

部署Bookinfo应用到Kubernetes集群：

```text
[root@master ~]# cd ServiceMesh/
[root@master ServiceMesh]# kubectl apply -f bookinfo/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```

查看Pod状态：

```text
[root@master ServiceMesh]# kubectl get pods
NAME                             READY  STATUS  RESTARTS  AGE
details-v1-79f774bdb9-m98sl      1/1    Running   0      46s
productpage-v1-6b746f74dc-snpf9  1/1    Running   0      46s
ratings-v1-b6994bb9-nmws8        1/1    Running   0      46s
reviews-v1-545db77b95-4rtn4      1/1    Running   0      46s
```

##### （2）启用对应用程序的外部访问

现在Bookinfo应用程序已成功运行，需要使应用程序可以从外部访问，可以用Istio Gateway来实现这个目标。

使用网关为网格来管理入站和出站流量，可以指定要进入或流出网格的流量。网关配置被用于运行在网格边界的独立Envoy代理，而不是服务工作负载的sidecar代理。

与Kubernetes Ingress API这种控制进入系统流量的其他机制不同，Istio网关充分利用了流量路由的强大能力和灵活性。Istio的网关资源可以配置4-6层的负载均衡属性，如对外暴露的端口、TLS设置等。作为替代应用层流量路由（L7）到相同的API资源，绑定一个常规的Istio虚拟服务到网关，这样就可以像管理网格中其他数据平面的流量一样去管理网关流量。

网关主要用于管理进入的流量，也可以配置出口网关。出口网关为流出网格的流量配置一个专用的出口节点，这可以限制哪些服务可以或应该访问外部网络，或者启用出口流量安全控制为网格添加安全性。

Gateway配置文件如下：

```yaml
[root@master ServiceMesh]# cat bookinfo-gateway.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: bookinfo-gateway  # 网关的名称
spec:
  selector:
    istio: ingressgateway  # 使用 Istio 默认的 Ingress 网关控制器
  servers:
  - port:
      number: 80  # 监听的端口号为 80
      name: http
      protocol: HTTP
    hosts:
    - "*"  # 接受所有主机的流量
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: bookinfo  # 虚拟服务的名称
spec:
  hosts:
  - "*"  # 匹配所有主机
  gateways:
  - bookinfo-gateway  # 使用之前定义的 "bookinfo-gateway" 网关
  http:
  - match:
    - uri:
        exact: /productpage  # 精确匹配 URI "/productpage"
    - uri:
        prefix: /static  # 匹配以 "/static" 开头的 URI
    - uri:
        exact: /login  # 精确匹配 URI "/login"
    - uri:
        exact: /logout  # 精确匹配 URI "/logout"
    - uri:
        prefix: /api/v1/products  # 匹配以 "/api/v1/products" 开头的 URI
    route:
    - destination:
        host: productpage  # 将匹配到的请求路由到 "productpage" 服务
        port:
          number: 9080  # 使用端口号 9080
```

这个网关指定所有HTTP流量通过80端口流入网格，然后把网关绑定到虚拟服务上。

为应用程序定义Ingress网关：

```text
[root@master ServiceMesh]# kubectl apply -f bookinfo-gateway.yaml
gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created
```

确认网关创建完成：

```text
[root@master ServiceMesh]# kubectl get gateway
NAME               AGE
bookinfo-gateway       32s
```

查看Ingress Gateway：

```text
[root@master ServiceMesh]# kubectl get svc -n istio-system
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
istio-egressgateway    ClusterIP      10.101.130.107   <none>        80/TCP,443/TCP  30m
istio-ingressgateway   LoadBalancer   10.102.250.102   <pending>     15021:32634/TCP,80:22092/TCP,443:30119/TCP,31400:30282/TCP,15443:32486/TCP           30m
```

可以看到Gateway 80端口对应的NodePort端口是22092，在浏览器上通过http://master_IP:22092/productpage访问Bookinfo应用，如图所示：

[图片已失效 - 原始来源: douxuedu.com]
图1

##### （3）生产测试

使用Curl工具每秒向Bookinfo应用发送1个请求，模拟用户流量：

```bash
[root@master ServiceMesh]# vi curl.sh
#!/bin/bash
while true
do
  curl http://10.24.2.5:22092/productpage >/dev/null 2>&1
  sleep 1
done
```

后台运行脚本：

```text
[root@master ServiceMesh]# chmod +x curl.sh
[root@master ServiceMesh]# bash curl.sh &
[1] 2924
```

#### 3. 启用Istio

##### （1）在productpage启用Istio

在productpage微服务中，启用Istio。这个应用的其他部分会继续照原样运行。可以一个微服务一个微服务的逐步启用Istio。启用Istio在微服务中是无侵入的，不用修改微服务代码或者破坏应用，它也能够持续运行并且为用户请求服务。

在使用Istio控制Bookinfo版本路由之前，需要在目标规则中定义好可用的版本。目标规则是Istio流量路由功能的关键部分。可以将虚拟服务视为将流量如何路由到给定目标地址，然后使用目标规则来配置该目标的流量。在评估虚拟服务路由规则之后，目标规则将应用于流量的“真实”目标地址。

编写目标规则配置文件：

```bash
[root@master ServiceMesh]# cat destination-rule-all.yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: productpage
spec:
  host: productpage
  subsets:
  - name: v1
    labels:
      version: v1
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: ratings
spec:
  host: ratings
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v2-mysql
    labels:
      version: v2-mysql
  - name: v2-mysql-vm
    labels:
      version: v2-mysql-vm
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: details
spec:
  host: details
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2

"""
针对 productpage 服务的 DestinationRule：
metadata 部分定义了资源的元数据，包括资源名称为 productpage。
spec 部分包含了规则的详细配置，其中：
host 指定了规则应用于哪个服务，这里是 productpage 服务。
subsets 定义了服务的子集，包含一个子集名为 v1，并指定了该子集的标签 version: v1。这意味着这个规则将流量路由到 productpage 服务的 v1 子集，该子集具有标签 version: v1。

针对 reviews 服务的 DestinationRule：
metadata 部分定义了资源的元数据，包括资源名称为 reviews。
spec 部分包含了规则的详细配置，其中：
host 指定了规则应用于哪个服务，这里是 reviews 服务。
subsets 定义了服务的多个子集，分别是 v1、v2 和 v3，并为每个子集指定了相应的标签。这意味着这个规则可以将流量路由到 reviews 服务的不同子集，根据子集的标签选择不同的版本。

针对 ratings 服务的 DestinationRule：
metadata 部分定义了资源的元数据，包括资源名称为 ratings。
spec 部分包含了规则的详细配置，其中：
host 指定了规则应用于哪个服务，这里是 ratings 服务。
subsets 定义了服务的多个子集，分别是 v1、v2、v2-mysql 和 v2-mysql-vm，并为每个子集指定了相应的标签。这意味着这个规则可以将流量路由到 ratings 服务的不同子集，根据子集的标签选择不同的版本或实现方式。

针对 details 服务的 DestinationRule：
metadata 部分定义了资源的元数据，包括资源名称为 details。
spec 部分包含了规则的详细配置，其中：
host 指定了规则应用于哪个服务，这里是 details 服务。
subsets 定义了服务的多个子集，分别是 v1 和 v2，并为每个子集指定了相应的标签。这意味着这个规则可以将流量路由到 details 服务的不同子集，根据子集的标签选择不同的版本。
"""
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: productpage  # DestinationRule 的名称，用于 productpage 服务
spec:
  host: productpage  # 指定该规则适用于哪个服务，这里是 productpage 服务
  subsets:  # 定义服务的子集
  - name: v1  # 子集的名称，表示版本 v1
    labels:
      version: v1  # 为该子集指定标签，用于流量控制
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: reviews  # DestinationRule 的名称，用于 reviews 服务
spec:
  host: reviews  # 指定该规则适用于哪个服务，这里是 reviews 服务
  subsets:  # 定义服务的子集
  - name: v1  # 子集的名称，表示版本 v1
    labels:
      version: v1  # 为该子集指定标签，用于流量控制
  - name: v2  # 子集的名称，表示版本 v2
    labels:
      version: v2  # 为该子集指定标签，用于流量控制
  - name: v3  # 子集的名称，表示版本 v3
    labels:
      version: v3  # 为该子集指定标签，用于流量控制
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: ratings  # DestinationRule 的名称，用于 ratings 服务
spec:
  host: ratings  # 指定该规则适用于哪个服务，这里是 ratings 服务
  subsets:  # 定义服务的子集
  - name: v1  # 子集的名称，表示版本 v1
    labels:
      version: v1  # 为该子集指定标签，用于流量控制
  - name: v2  # 子集的名称，表示版本 v2
    labels:
      version: v2  # 为该子集指定标签，用于流量控制
  - name: v2-mysql  # 子集的名称，表示版本 v2-mysql
    labels:
      version: v2-mysql  # 为该子集指定标签，用于流量控制
  - name: v2-mysql-vm  # 子集的名称，表示版本 v2-mysql-vm
    labels:
      version: v2-mysql-vm  # 为该子集指定标签，用于流量控制
---
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: details  # DestinationRule 的名称，用于 details 服务
spec:
  host: details  # 指定该规则适用于哪个服务，这里是 details 服务
  subsets:  # 定义服务的子集
  - name: v1  # 子集的名称，表示版本 v1
    labels:
      version: v1  # 为该子集指定标签，用于流量控制
  - name: v2  # 子集的名称，表示版本 v2
    labels:
      version: v2  # 为该子集指定标签，用于流量控制
```

创建默认目标规则：

```text
[root@master ServiceMesh]# kubectl apply -f destination-rule-all.yaml
[root@master ServiceMesh]# kubectl get destinationrule
NAME         HOST          AGE
details      details       21s
productpage  productpage   21s
ratings      ratings       21s
reviews      reviews       21s
```

重新部署productpage微服务，启用Istio：

```bash
[root@master ServiceMesh]# cat bookinfo/bookinfo.yaml | istioctl kube-inject -f - | kubectl apply -l app=productpage -f -
service/productpage unchanged
deployment.apps/productpage-v1 configured
```

检查productpage的Pod并且查看每个副本的两个容器。第一个容器是微服务本身的，第二个是连接到它的Sidecar代理：

```text
[root@master ServiceMesh]# kubectl get pods
NAME                             READY      STATUS    RESTARTS       AGE
details-v1-79f774bdb9-np7tt       1/1       Running     0            8m9s
productpage-v1-599f9dc7b8-btdqz   2/2       Running     0            43s
ratings-v1-b6994bb9-nkq7z         1/1       Running     0            8m9s
reviews-v1-545db77b95-xwnwf       1/1       Running     0            8m9s
```

Kubernetes采取无侵入的和逐步的滚动更新方式用启用Istio的Pod替换了原有的Pod。Kubernetes只有在新的Pod开始运行的时候才会终止老的Pod，它透明地将流量一个一个地切换到新的Pod上。也就是说，它不会在声明一个新的Pod之前结束一个或者以上的Pod。这些操作都是为了防止破坏应用，因此在注入Istio的过程中应用能够持续工作。

在浏览器上登录Grafana（http://master_IP:33000），如图2所示：

[图片已失效 - 原始来源: douxuedu.com]
图2

依次点击左侧导航栏的“Dashboards” →“Manage”进入Dashboard管理界面，如图3所示：

[图片已失效 - 原始来源: douxuedu.com]
图3

选择Istio Mesh Dashboard，如图4所示：

[图片已失效 - 原始来源: douxuedu.com]
图4

切换到Istio Service Dashboard仪表盘，在Service中选择productpage，如图5所示：

[图片已失效 - 原始来源: douxuedu.com]
图5

##### （2）在所有微服务中启用Istio

所有服务启用Istio：

```text
[root@master ServiceMesh]# cat bookinfo/bookinfo.yaml | istioctl kube-inject -f - | kubectl apply -l app!=productpage -f -
```

查看应用程序Pod，现在每个Pod的两个容器，一个容器是微服务本身，另一个是连接到它的Sidecar代理：

```text
[root@master ServiceMesh]# kubectl get pods
NAME                               READY     STATUS    RESTARTS        AGE
details-v1-6df75fb475-4stwg        2/2        Running     0            70s
productpage-v1-599f9dc7b8-fprwf    2/2        Running     0            5m2s
ratings-v1-c7b9dfddc-rlmk4         2/2        Running     0            70s
reviews-v2-855b56b89-57k67         2/2        Running     0            64s
```

再次查看Istio Mesh Dashboard，会发现当前命名空间下所有服务都会出现在服务列表中，如图6所示：

[图片已失效 - 原始来源: douxuedu.com]
图6

访问Kiali控住台（http://master_IP:20001），如图7所示：

[图片已失效 - 原始来源: douxuedu.com]
图7

通过可视化界面来查看应用程序的拓扑结构，点击“Graph”按钮，在Namespace下拉菜单中选择命名空间default，然后在Display下拉菜单中选中“Traffic Animation”和“Idle Nodes”复选框，就可以看到实时流量动画。如图8所示：

[图片已失效 - 原始来源: douxuedu.com]
图8

reviews微服务v1版本不会调用ratings服务，所以图中ratings服务无流量通过。

##### （3）监控Istio

访问Prometheus控制台（http://master_IP:30090），如图9所示：

[图片已失效 - 原始来源: douxuedu.com]
图9

在Expression输入框中输入要查询的参数，然后点击Execute按钮即可在Console中查看查询结果。

查询请求时采用istio_requests_total指标，这是一个标准的Istio指标。

如查询命名空间的所有请求（istio_requests_total{destination_service_namespace=“default”, reporter=“destination”}），如图10所示：

[图片已失效 - 原始来源: douxuedu.com]
图10

查询reviews微服务的请求（istio_requests_total{destination_service_namespace=“default”, reporter=“destination”,destination_service_name=“reviews”}），如图11所示：

[图片已失效 - 原始来源: douxuedu.com]
图11

#### 4. 灰度发布

##### （1）部署新版本服务

将v2、v3版本的reviews服务部署到集群中，均为单一副本。新版本的reviews可以正常工作后，实际生产流量将开始到达该服务。在当前的设置下，50%的流量将到达旧版本（1个旧版本的Pod），而另外50%的流量将到达新版本（1个新版本Pod）。

部署v2版本的reviews微服务并开启Istio：

```text
[root@master ServiceMesh]# cat bookinfo/reviews-v2.yaml | istioctl kube-inject -f - | kubectl apply -f -
[root@master ServiceMesh]# cat bookinfo/reviews-v3.yaml | istioctl kube-inject -f - | kubectl apply -f -
```

查看Pod：

```text
[root@master ServiceMesh]# kubectl get pods
NAME                              READY       STATUS    RESTARTS       AGE
details-v1-6df75fb475-4stwg        2/2        Running     0            5m5s
productpage-v1-599f9dc7b8-fprwf    2/2        Running     0            8m2s
ratings-v1-c7b9dfddc-rlmk4         2/2        Running     0            5m5s
reviews-v2-855b56b89-57k67         2/2        Running     0            5m5s
reviews-v2-855b56b89-49jkx         2/2        Running     0            34s
reviews-v3-7b76df6bbb-f6hhp        2/2        Running     0            29s
```

访问Bookinfo应用程序页面，如图12所示：

[图片已失效 - 原始来源: douxuedu.com]
图12

观察评级上的星标，发现有时返回的页面带有黑色星标（v2版本、大约三分之一的时间），有时带有红色星标（v3版本、大约三分之一的时间），有时不带星标（v1版本、大约三分之一的时间），这是因为没有明确的默认服务版本和路由，Istio将以轮询方式将请求路由到所有可用版本，所以三种评分结果出现的概率均为三分之一。

查看实时流量监控，如图13所示：

[图片已失效 - 原始来源: douxuedu.com]
图13

可以看到，v2和v3版本的reviews微服务已正常工作，因v2和v3版本会调用ratings服务，所以图中ratings服务也有流量通过。

##### （2）请求路由

Bookinfo应用程序包含四个独立的微服务，每个微服务都有多个版本。其中一个微服务reviews的3个不同版本已经部署并同时运行。因为没有明确的默认服务版本和路由，Istio将以轮询方式将请求路由到所有可用版本。这样将导致在浏览器中访问Bookinfo应用程序时，输出有时包含星级评分，有时则不包含。

Kubernetes方式下控制流量分配需要调整每个Deployment的副本数目。例如，将10％的流量发送到金丝雀版本（v2），v1和v2的副本可以分别设置为9和1。由于在启用Istio后不再需要保持副本比例，所以可以安全地设置Kubernetes HPA来管理三个版本Deployment的副本：

```text
[root@master ServiceMesh]# kubectl autoscale deployment reviews-v1 --cpu-percent=50 --min=1 --max=10
[root@master ServiceMesh]# kubectl autoscale deployment reviews-v2 --cpu-percent=50 --min=1 --max=10
[root@master ServiceMesh]# kubectl autoscale deployment reviews-v3 --cpu-percent=50 --min=1 --max=10
```

如果要仅路由到一个版本，请为微服务设置默认版本的Virtual Service。在这种情况下，Virtual Service将所有流量路由到每个微服务的v1版本。

默认请求路由配置文件如下：

```bash
[root@master ServiceMesh]# vi virtual-service-all-v1.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings
spec:
  hosts:
  - ratings
  http:
  - route:
    - destination:
        host: ratings
        subset: v1
﹉（“﹉”请学生自行手打，不要复制）
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details
spec:
  hosts:
  - details
  http:
  - route:
    - destination:
        host: details
        subset: v1

'''
这个配置文件 virtual-service-all-v1.yaml 包含了多个 Istio VirtualService 资源的定义，它们用于指定流量的路由规则，将流量路由到服务的特定子集。

针对 productpage 服务的 VirtualService：
metadata 部分定义了资源的元数据，包括资源名称为 productpage。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 productpage 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由，将请求发送到 productpage 服务的 v1 子集。

针对 reviews 服务的 VirtualService：
metadata 部分定义了资源的元数据，包括资源名称为 reviews。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 reviews 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由，将请求发送到 reviews 服务的 v1 子集。

针对 ratings 服务的 VirtualService：
metadata 部分定义了资源的元数据，包括资源名称为 ratings。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 ratings 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由，将请求发送到 ratings 服务的 v1 子集。

针对 details 服务的 VirtualService：
metadata 部分定义了资源的元数据，包括资源名称为 details。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 details 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由，将请求发送到 details 服务的 v1 子集。
'''
```

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: productpage  # VirtualService 的名称，用于 productpage 服务
spec:
  hosts:
  - productpage  # 适用于 productpage 服务
  http:
  - route:
    - destination:
        host: productpage  # 将请求发送到哪个目标主机，这里是 productpage 服务
        subset: v1  # 将请求路由到 productpage 服务的 v1 子集
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews  # VirtualService 的名称，用于 reviews 服务
spec:
  hosts:
  - reviews  # 适用于 reviews 服务
  http:
  - route:
    - destination:
        host: reviews  # 将请求发送到哪个目标主机，这里是 reviews 服务
        subset: v1  # 将请求路由到 reviews 服务的 v1 子集
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: ratings  # VirtualService 的名称，用于 ratings 服务
spec:
  hosts:
  - ratings  # 适用于 ratings 服务
  http:
  - route:
    - destination:
        host: ratings  # 将请求发送到哪个目标主机，这里是 ratings 服务
        subset: v1  # 将请求路由到 ratings 服务的 v1 子集
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: details  # VirtualService 的名称，用于 details 服务
spec:
  hosts:
  - details  # 适用于 details 服务
  http:
  - route:
    - destination:
        host: details  # 将请求发送到哪个目标主机，这里是 details 服务
        subset: v1  # 将请求路由到 details 服务的 v1 子集
```

配置默认请求路由：

```text
[root@master ServiceMesh]# kubectl apply -f virtual-service-all-v1.yaml
virtualservice.networking.istio.io/productpage created
virtualservice.networking.istio.io/reviews created
virtualservice.networking.istio.io/ratings created
virtualservice.networking.istio.io/details created
```

现在已将Istio配置为路由到Bookinfo微服务的v1版本，最重要的是reviews服务的v1版本。

在浏览器中打开Bookinfo站点，如图14所示：

[图片已失效 - 原始来源: douxuedu.com]
图14

此时无论刷新多少次，页面的评分部分都不会显示评级星标。这是因为Istio被配置为将评分服务的所有流量路由到v1版本的reviews，而此版本的服务不调用星级评分服务。

查看实时流量监控，也可以看到此时无流量流向v2和v3版本，如图15所示：

[图片已失效 - 原始来源: douxuedu.com]
图15

##### （3）流量转移

使用下面的命令把50%的流量从reviews:v1转移到reviews:v3（金丝雀版本）：

```bash
[root@master ServiceMesh]# vi virtual-service-reviews-50-50.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50
[root@master ServiceMesh]# kubectl apply -f virtual-service-reviews-50-50.yaml
virtualservice.networking.istio.io/reviews configured
'''
个配置文件 virtual-service-reviews-50-50.yaml 定义了一个 Istio VirtualService，用于服务 reviews 的流量分发规则，将流量平均分发到 reviews 服务的两个不同子集中，每个子集的权重为 50%，实现了一种 50-50 的分流策略。
具体解释如下：
metadata 部分定义了资源的元数据，包括资源名称为 reviews，这是 VirtualService 的名称。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 reviews 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由。
第一个 route 部分指定了将请求发送到 reviews 服务的 v1 子集，通过 destination 部分配置。权重为 50，表示将 50% 的流量路由到 v1 子集。
第二个 route 部分指定了将请求发送到 reviews 服务的 v3 子集，通过 destination 部分配置。权重为 50，表示将另外的 50% 的流量路由到 v3 子集。
这意味着所有针对 reviews 服务的流量会被均匀分为两部分，其中 50% 的流量将被发送到 v1 子集，另外的 50% 将被发送到 v3 子集。这种配置可用于实现 A/B 测试、渐进式升级等流量分发策略。
'''
```

当规则设置生效后，Istio将确保只有50%的请求发送到金丝雀版本，无论每个版本的运行副本数量是多少。

刷新浏览器中的/productpage页面，因为reviews的v3版本可以访问带星级评价服务，而v1版本不能，所以大约有50%的几率会看到页面中带红色星级的评价内容，如图16所示：

[图片已失效 - 原始来源: douxuedu.com]
图16

在Kiali上查看实时流量监控，可以看到流量已经流向了v3版本，如图1718所示：

[图片已失效 - 原始来源: douxuedu.com]
图17

点击productpage服务与reviews服务之间的连线，在右侧可以看到每秒发送到reviews服务的http请求为0.96，如图所示：

[图片已失效 - 原始来源: douxuedu.com]
图18

点击reviews服务和v1版本之间的连线，可以看到v1版本每秒接收到的http请求为0.49，如图19所示：

[图片已失效 - 原始来源: douxuedu.com]
图19

点击reviews服务和v3版本之间的连线，可以看到v3版本每秒接收到的http请求为0.47，如图20所示：

[图片已失效 - 原始来源: douxuedu.com]
图20

说明流向reviews服务的流量各有50%流向了v1和v3，路由规则应用成功。

假如认为reviews:v3微服务已经稳定，可以通过应用Virtual Service规则将100%的流量路由reviews:v3：

```bash
[root@master ServiceMesh]# vi virtual-service-reviews-v3.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v3
[root@master ServiceMesh]# kubectl apply -f virtual-service-reviews-v3.yaml
virtualservice.networking.istio.io/reviews configured
'''
这个配置文件 virtual-service-reviews-v3.yaml 定义了一个 Istio VirtualService，用于服务 reviews 的流量路由规则，将所有流量都路由到 reviews 服务的 v3 子集。
具体解释如下：
metadata 部分定义了资源的元数据，包括资源名称为 reviews，这是 VirtualService 的名称。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 reviews 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由。
destination 部分指定了将请求发送到 reviews 服务的 v3 子集，这是通过 subset 字段配置的。这意味着所有针对 reviews 服务的流量都将被路由到 v3 子集。
这个配置文件的效果是将所有的流量发送到 reviews 服务的 v3 子集，而不考虑其他版本的子集。这可以用于实现全流量升级或针对某个特定子集进行流量测试。
'''
```

再次刷新/productpage时，将始终看到带有红色星级评分的书评。

查看实时流量，发现流量全部流向v3，如图21所示：

[图片已失效 - 原始来源: douxuedu.com]
图21

#### 5. 流量镜像

流量镜像，也称为影子流量，是一个以尽可能低的风险为生产带来变化的强大的功能。镜像会将实时流量的副本发送到镜像服务。镜像流量发生在主服务的关键请求路径之外。

初始化默认路由规则，将所有流量路由到服务的v1版本：

```text
[root@master ServiceMesh]# kubectl apply -f virtual-service-all-v1.yaml
```

改变reviews服务的流量规则，将v1版本的流量镜像到v2版本：

```bash
[root@master ServiceMesh]# vi virtual-service-mirroring.yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
    - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 100
    mirror:
        host: reviews
        subset: v2
[root@master ServiceMesh]# kubectl apply -f virtual-service-mirroring.yaml
virtualservice.networking.istio.io/reviews configured
'''
这个配置文件 virtual-service-mirroring.yaml 定义了一个 Istio VirtualService，用于实现流量镜像，即将流量同时发送到 reviews 服务的 v1 子集和 v2 子集。
具体解释如下：
metadata 部分定义了资源的元数据，包括资源名称为 reviews，这是 VirtualService 的名称。
spec 部分包含了规则的详细配置，其中：
hosts 列出了适用于哪个主机，这里是 reviews 服务。
http 定义了 HTTP 请求的路由规则，包含一个 route 部分，该路由部分指定了请求应该如何路由。
第一个 route 部分指定了将请求发送到 reviews 服务的 v1 子集，通过 destination 部分配置。权重为 100，表示将 100% 的流量路由到 v1 子集。
mirror 部分指定了流量镜像，将请求的镜像发送到 reviews 服务的 v2 子集。这意味着 100% 的流量也会被复制到 v2 子集，但不会影响正常请求的路由。
这个配置的效果是，所有针对 reviews 服务的请求将被发送到 v1 子集，同时也将在后台镜像到 v2 子集，以便进行流量分析、测试或监视。这对于逐步升级和版本验证非常有用，因为它允许你在不中断正常流量的情况下，观察新版本的行为。
'''
```

#### 6. 分布式追踪

分布式追踪可以让用户对跨多个分布式服务网格的1个请求进行追踪分析。这样进而可以通过可视化的方式更加深入地了解请求的延迟，序列化和并行度。

Istio利用Envoy的分布式追踪功能提供了开箱即用的追踪集成。确切地说，Istio提供了安装各种追踪后端服务的选项，并且通过配置代理来自动发送追踪Span到追踪后端服务。

登录Jaeger控制台（http://master_IP:30686），如图22所示：

[图片已失效 - 原始来源: douxuedu.com]
图22

从仪表盘左边面板的Service下拉列表中选择“productpage.default”，然后点击“Find Traces”，如图23所示：

[图片已失效 - 原始来源: douxuedu.com]
图23

点击位于最上面的最近一次追踪，查看对应最近一次访问/productpage的详细信息，如图24所示：

[图片已失效 - 原始来源: douxuedu.com]
图24

追踪信息由一组Span组成，每个Span对应一个Bookinfo Service。这些Service在执行/productpage请求时被调用，或是Istio内部组件。
