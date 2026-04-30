**【题目27】Helm发布应用--部署Redis集群 [1.5分]**

使用提供的Chart包redis-cluster-7.5.0.tgz在Kubernetes集群default命名空间下部署Redis集群，要求集群架构为3主3从，Release名称为redis。

完成后提交master节点的用户名、密码和IP地址到答题框。（需要用到的软件包：redis-cluster-7.5.0.tgz和bitnami_redis-cluster_6.2.6-debian-10-r193.tar）

解压chat包

[root@k8s-master-node1 ~]# tar -zxvf redis-cluster-7.5.0.tgz

双节点上传镜像

[root@k8s-master-node1 ~]# docker load -i bitnami_redis-cluster_6.2.6-debian-10-r193.tar

[root@k8s-worker-node1 ~]# docker load -i bitnami_redis-cluster_6.2.6-debian-10-r193.tar

[root@k8s-master-node1 ~]# cd redis-cluster/

配置nfs

[root@k8s-master-node1 redis-cluster]# mkdir -p /redis/redis{1..6}

[root@k8s-master-node1 redis-cluster]# ls /redis/

redis1  redis2  redis3  redis4  redis5  redis6

[root@k8s-master-node1 redis-cluster]# cat /etc/exports

/redis *(rw)

[root@k8s-master-node1 redis-cluster]# systemctl restart rpcbind

[root@k8s-master-node1 redis-cluster]# systemctl restart nfs

[root@k8s-master-node1 redis-cluster]# showmount -e

Export list for k8s-master-node1:

/redis *

helm里面用户是1001所以要给1001用户权限

[root@k8s-master-node1 redis-cluster]# chown -R 1001:root /redis/

[root@k8s-master-node1 redis-cluster]# ll /redis/

总用量 0

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis1

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis2

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis3

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis4

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis5

drwxr-xr-x 2 1001 root 6 4月  15 17:45 redis6

#创建pv

[root@k8s-master-node1 redis-cluster]# cat pv.yaml

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis1
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
    path: /redis/redis1
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis2
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
    path: /redis/redis2
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis3
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
    path: /redis/redis3
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis4
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
    path: /redis/redis4
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis5
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
    path: /redis/redis5
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis6
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: redis
  nfs:
    server: 192.168.10.105
		path: /redis/redis6
```

[root@k8s-master-node1 redis-cluster]# kubectl apply -f pv.yaml

persistentvolume/redis1 created

persistentvolume/redis2 created

persistentvolume/redis3 created

persistentvolume/redis4 created

persistentvolume/redis5 created

persistentvolume/redis6 created

[root@k8s-master-node1 redis-cluster]# kubectl get pv

NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE

redis1   10Gi       RWO            Retain           Available           redis                   3s

redis2   10Gi       RWO            Retain           Available           redis                   3s

redis3   10Gi       RWO            Retain           Available           redis                   3s

redis4   10Gi       RWO            Retain           Available           redis                   3s

redis5   10Gi       RWO            Retain           Available           redis                   3s

redis6   10Gi       RWO            Retain           Available           redis                   3s

[root@k8s-master-node1 redis-cluster]# cat values.yaml

```yaml
## @param global.redis.password Redis&trade; password (overrides `password`)
##
global:
  imageRegistry: ""  #修改为""
  ## E.g.
  ## imagePullSecrets:
  ##   - myRegistryKeySecretName
  ##
  imagePullSecrets: []
  storageClass: "redis"  #设置存储类名称
  redis:  #设置redis密码
    password: "123456"

## @section Redis&trade; Cluster Common parameters
……
```

#部署redis

[root@k8s-master-node1 redis-cluster]# helm install redis .

NAME: redis

LAST DEPLOYED: Sat Apr 15 17:51:49 2023

NAMESPACE: default

STATUS: deployed

REVISION: 1

TEST SUITE: None

NOTES:

CHART NAME: redis-cluster

CHART VERSION: 7.5.0

APP VERSION: 6.2.6** Please be patient while the chart is being deployed **

To get your password run:

    export REDIS_PASSWORD=$(kubectl get secret --namespace "default" redis-redis-cluster -o jsonpath="{.data.redis-password}" | base64 --decode)

You have deployed a Redis&trade; Cluster accessible only from within you Kubernetes Cluster.INFO: The Job to create the cluster will be created.To connect to your Redis&trade; cluster:

1. Run a Redis&trade; pod that you can use as a client:

kubectl run --namespace default redis-redis-cluster-client --rm --tty -i --restart='Never' \

 --env REDIS_PASSWORD=$REDIS_PASSWORD \

--image docker.io/bitnami/redis-cluster:6.2.6-debian-10-r193 -- bash

2. Connect using the Redis&trade; CLI:

redis-cli -c -h redis-redis-cluster -a $REDIS_PASSWORD

[root@k8s-master-node1 redis-cluster]# kubectl get pvc

NAME                               STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE

redis-data-redis-redis-cluster-0   Bound    redis3   10Gi       RWO            redis          10s

redis-data-redis-redis-cluster-1   Bound    redis4   10Gi       RWO            redis          10s

redis-data-redis-redis-cluster-2   Bound    redis1   10Gi       RWO            redis          10s

redis-data-redis-redis-cluster-3   Bound    redis2   10Gi       RWO            redis          10s

redis-data-redis-redis-cluster-4   Bound    redis5   10Gi       RWO            redis          10s

redis-data-redis-redis-cluster-5   Bound    redis6   10Gi       RWO            redis          10s

[root@k8s-master-node1 redis-cluster]# kubectl get pod #等一分钟左右

NAME                    READY   STATUS    RESTARTS      AGE

redis-redis-cluster-0   1/1     Running   1 (47s ago)   109s

redis-redis-cluster-1   1/1     Running   1 (42s ago)   109s

redis-redis-cluster-2   1/1     Running   1 (47s ago)   109s

redis-redis-cluster-3   1/1     Running   1 (42s ago)   109s

redis-redis-cluster-4   1/1     Running   1 (42s ago)   109s

redis-redis-cluster-5   1/1     Running   1 (42s ago)   109s

检查

[root@k8s-master-node1 redis-cluster]# kubectl exec statefulset/redis-redis-cluster -- redis-cli -h 127.0.0.1 -a 123456 cluster info

Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.

cluster_state:ok

cluster_slots_assigned:16384

cluster_slots_ok:16384

cluster_slots_pfail:0

cluster_slots_fail:0

cluster_known_nodes:6

cluster_size:3

cluster_current_epoch:6

cluster_my_epoch:1

cluster_stats_messages_ping_sent:136

cluster_stats_messages_pong_sent:136

cluster_stats_messages_sent:272

cluster_stats_messages_ping_received:136

cluster_stats_messages_pong_received:134

cluster_stats_messages_received:270
