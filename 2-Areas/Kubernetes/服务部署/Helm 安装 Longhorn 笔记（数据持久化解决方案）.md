# Helm 安装 Longhorn 笔记（数据持久化解决方案）

Helm 安装 Longhorn 笔记（数据持久化解决方案）相关技术笔记。

**Helm 安装 Longhorn 笔记（数据持久化解决方案）**

环境如下

| 类别 | 详情 |
| :--- | :--- |
| Kubernetes 版本 | v1.34.2 |
| 操作系统镜像 | Ubuntu 22.04.5 2025.08.22 LTS (Cubic 2025-08-22 14:55) |
| 内核版本 | 6.8.0-87-generic |
| 容器运行时 | Docker 28.1.1（docker://28.1.1） |
| Helm 版本 | v3.14.4（GitCommit:81c902a123462fd4052bc5e9aa9c513c4c8fc142，GitTreeState:clean，GoVersion:go1.21.9） |

**一、安装命令**

| # 所有节点安装iscsid   sudo apt install -y open-iscsi   sudo systemctl enable --now iscsid |
| :--- |

| # Helm 安装 Longhorn（指定命名空间和自定义配置文件）   helm install longhorn longhorn/longhorn -n longhorn-volume -f longhorn-values.yaml |
| :--- |

**二、安装结果验证**

**1. Helm 部署状态**

| helm list -A |
| :--- |

输出结果：

| NAME | NAMESPACE | REVISION | UPDATED | STATUS | CHART | APP VERSION |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| longhorn | longhorn-volume | 1 | 2025-12-02 20:32:41.596072587 +0800 CST | deployed | longhorn-1.10.1 | v1.10.1 |

**2. 集群 Pod 状态（Longhorn 组件）**

| kubectl get pod -n longhorn-volume |
| :--- |

输出结果（所有组件均为 Running 状态，说明部署正常）：

| Plain TextNAME                                                READY   STATUS    RESTARTS   AGE   csi-attacher-5857549d6f-cgshm                       1/1     Running   0          25m   csi-attacher-5857549d6f-jkqp9                       1/1     Running   0          25m   csi-attacher-5857549d6f-pbjc7                       1/1     Running   0          25m   csi-provisioner-57f9d44448-5w2p7                    1/1     Running   0          25m   csi-provisioner-57f9d44448-m9fmw                    1/1     Running   0          25m   csi-provisioner-57f9d44448-xp2f9                    1/1     Running   0          25m   csi-resizer-547f8b9dc8-gtpd6                        1/1     Running   0          25m   csi-resizer-547f8b9dc8-n7nbs                        1/1     Running   0          25m   csi-resizer-547f8b9dc8-x5bvl                        1/1     Running   0          25m   csi-snapshotter-8558df8679-h57w9                    1/1     Running   0          25m   csi-snapshotter-8558df8679-whl7r                    1/1     Running   0          25m   csi-snapshotter-8558df8679-zwhxl                    1/1     Running   0          25m   engine-image-ei-3154f3aa-w8l6l                      1/1     Running   0          26m   instance-manager-3413983adef0042dc0770424fa469f3d   1/1     Running   0          25m   longhorn-csi-plugin-8dtbm                           3/3     Running   0          25m   longhorn-driver-deployer-676f7f6c5c-xzmwf           1/1     Running   0          26m   longhorn-manager-slqs8                              2/2     Running   0          26m   longhorn-ui-7c54575f4d-sn7lg                        1/1     Running   0          26m   longhorn-ui-7c54575f4d-z8b44                        1/1     Running   0          26m |
| :--- |

**3. 存储类（SC）状态**

| Bashkubectl get sc |
| :--- |

输出结果（Longhorn 已设为默认存储类）：

| Plain TextNAME                 PROVISIONER          RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE   longhorn (default)   driver.longhorn.io   Delete          Immediate           true                   26m   longhorn-static      driver.longhorn.io   Delete          Immediate           true                   68m |
| :--- |

**4. 测试 Longhorn 存储可用性（可选，验证功能正常）**

创建一个使用 Longhorn 存储的测试 Pod，确认存储可正常挂载：

| YAML# 1. 创建测试 PVC（使用 longhorn 存储类）   cat <<EOF | kubectl apply -f -   apiVersion: v1   kind: PersistentVolumeClaim   metadata:     name: test-longhorn-pvc   spec:     accessModes:       - ReadWriteOnce     storageClassName: longhorn     resources:       requests:         storage: 1Gi   EOF      # 2. 创建测试 Pod（挂载上述 PVC）   cat <<EOF | kubectl apply -f -   apiVersion: v1   kind: Pod   metadata:     name: test-longhorn-pod   spec:     containers:     - name: test       image: busybox       command: ["sleep", "3600"]       volumeMounts:       - name: test-volume         mountPath: /data     volumes:     - name: test-volume       persistentVolumeClaim:         claimName: test-longhorn-pvc   EOF# 3. 验证 Pod 状态（Running 表示存储挂载成功）   kubectl get pod test-longhorn-pod |
| :--- |

**三、自定义配置文件（longhorn-values.yaml）**

| YAMLreplicas: 3  # 数据副本数（生产必须 3，避免单节点故障）     #defaultSettings:     #  backupTarget: "s3://longhorn-backup@minio/default"  # 备份到 MinIO/S3     #  backupTargetCredentialSecret: "minio-backup-secret"  # 备份密钥（提前创建）   persistence:     defaultClass: true  # 是否将 Longhorn 设为默认存储类（生产建议 true，无需手动指定 storageClassName）     defaultFsType: ext4  # 卷文件系统类型（生产推荐 ext4，稳定兼容）     defaultClassReplicaCount: 1  # 卷默认副本数（生产必须 ≥3，跨节点冗余，避免单点故障）     reclaimPolicy: Delete  # PV 回收策略（生产建议 Delete：PVC 删除后自动删除 PV，减少垃圾资源）     volumeBindingMode: "Immediate"  # 卷绑定时机（Immediate：创建 PVC 立即绑定；WaitForFirstConsumer：等待第一个 Pod 调度时绑定，适合节点亲和性场景）     dataEngine: v1  # 数据引擎（v1 稳定版；v2 基于 SPDK，性能更高但目前是实验性，不建议生产用）      defaultSettings:     defaultDataPath: ~  # 节点本地数据存储路径（默认 /var/lib/longhorn/，生产建议改为独立磁盘路径，如 /data/longhorn）     storageOverProvisioningPercentage: 100  # 存储超配比例（默认 100：不超配，生产建议保留，避免磁盘满溢）     storageMinimalAvailablePercentage: 25  # 磁盘最小可用空间百分比（默认 25%：磁盘可用空间低于 25% 时停止调度新卷，防止磁盘满）     autoSalvage: ~  # 自动修复故障卷（默认 true：所有副本故障时自动尝试 salvage，生产建议保留）     autoDeletePodWhenVolumeDetachedUnexpectedly: ~  # 卷意外卸载时自动删除 Pod（默认 true：Pod 重启后自动重新挂载卷，生产建议保留）     logLevel: ~  # 日志级别（默认 Info，故障排查时可改为 Debug）     backupTarget: ~  # 备份目标地址（生产必须配置：如 S3/MinIO 地址，格式 s3://bucket-name@region/path）     backupTargetCredentialSecret: ~  # 备份认证密钥（需提前创建 K8s Secret，存储 S3/MinIO 的 accessKey/secretKey）      service:     ui:       # -- Service type for Longhorn UI. (Options: "ClusterIP", "NodePort", "LoadBalancer", "Rancher-Proxy")       type: NodePort       # -- NodePort port number for Longhorn UI. When unspecified, Longhorn selects a free port between 30000 and 32767.       nodePort: 38080       # -- Annotation for the Longhorn UI service.       annotations: {}       ## If you want to set annotations for the Longhorn UI service, delete the `{}` in the line above       ## and uncomment this example block       #  annotation-key1: "annotation-value1"       #  annotation-key2: "annotation-value2"     manager:       # -- Service type for Longhorn Manager.       type: ClusterIP       # -- NodePort port number for Longhorn Manager. When unspecified, Longhorn selects a free port between 30000 and 32767.       nodePort: ""      image:     longhorn:       engine:         registry: ""         repository: longhornio/longhorn-engine         tag: v1.10.1       manager:         registry: ""         repository: longhornio/longhorn-manager         tag: v1.10.1       ui:         registry: ""         repository: longhornio/longhorn-ui         tag: v1.10.1       instanceManager:         registry: ""         repository: longhornio/longhorn-instance-manager         tag: v1.10.1       shareManager:         registry: ""         repository: longhornio/longhorn-share-manager         tag: v1.10.1       backingImageManager:         registry: ""         repository: longhornio/backing-image-manager         tag: v1.10.1       supportBundleKit:         registry: ""         repository: longhornio/support-bundle-kit         tag: v0.0.71     csi:       attacher:         registry: ""         repository: longhornio/csi-attacher         tag: v4.10.0-20251030       provisioner:         registry: ""         repository: longhornio/csi-provisioner         tag: v5.3.0-20251030       nodeDriverRegistrar:         registry: ""         repository: longhornio/csi-node-driver-registrar         tag: v2.15.0-20251030       resizer:         registry: ""         repository: longhornio/csi-resizer         tag: v1.14.0-20251030       snapshotter:         registry: ""         repository: longhornio/csi-snapshotter         tag: v8.4.0-20251030       livenessProbe:         registry: ""         repository: longhornio/livenessprobe         tag: v2.17.0-20251030     pullPolicy: IfNotPresent |
| :--- |

![[1764748769717-d59bc4c9-0b47-4a30-9cbe-3089d3f86b31.png]]

![[1764748770046-e4e2acd0-6cdd-471a-b3c5-b2f5c273b260.png]]

**参考文档**

Longhorn 官方文档：[https://longhorn.io/docs/](https://longhorn.io/docs/)
