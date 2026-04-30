# Ceph 核心组件

Ceph 分布式存储系统的核心组件介绍。

+ **Monitors**

  监视器，维护集群状态的多种映射，同时提供认证和日志记录服务，包括有关 monitor 节点端到端的信息，其中包括 Ceph 集群 ID，监控主机名和 IP 以及端口。并且存储当前版本信息以及最新更改信息，通过 ceph mon dump 查看 monitor map。

+ **MDS（Metadata Server）**

  Ceph 元数据，主要保存的是 Ceph 文件系统的元数据。注意：ceph 的块存储和 ceph 对象存储都不需要 MDS。

+ **OSD**

  即对象存储守护程序，但是它并非针对对象存储。是物理磁盘驱动器，将数据以对象的形式存储到集群中的每个节点的物理磁盘上。OSD 负责存储数据、处理数据复制、恢复、回（Backfilling）、再平衡。完成存储数据的工作绝大多数是由 OSD daemon 进程实现。在构建 Ceph OSD 的时候，建议采用 SSD 磁盘以及 xfs 文件系统来格式化分区。此外 OSD 还对其它 OSD 进行心跳检测，检测结果汇报给 Monitor。

+ **RADOS**

  Reliable Autonomic Distributed Object Store。RADOS 是 ceph 存储集群的基础。在 ceph 中，所有数据都以对象的形式存储，并且无论什么数据类型，RADOS 对象存储都将负责保存这些对象。RADOS 层可以确保数据始终保持一致。

+ **librados**

  librados 库，为应用程度提供访问接口。同时也为块存储、对象存储、文件系统提供原生的接口。

+ **RADOSGW**

  网关接口，提供对象存储服务。它使用 librgw 和 librados 来实现允许应用程序与 Ceph 对象存储建立连接。并且提供 S3 和 Swift 兼容的 RESTful API 接口。

+ **RBD**

  块设备，它能够自动精简配置并可调整大小，而且将数据分散存储在多个 OSD 上。

+ **CephFS**

  Ceph 文件系统，与 POSIX 兼容的文件系统，基于 librados 封装原生接口。
