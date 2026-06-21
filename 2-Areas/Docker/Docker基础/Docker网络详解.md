# 默认网络
nicolaka/netshoot 测试网络的容器

"默认bridge网络"：默认为172.17.0.0/16这个网段，不包含容器间的dns解析功能

"host网络"：和主机共享所有网卡

"none网络"：不给容器分配网卡，无法与外界进行任何通信

```bash
#正常来说docker安装完成后默认拥有三个网络
#而我们使用docker run启动容器时不带任何参数，默认就是使用bridge这个网络
docker network list
NETWORK ID     NAME                DRIVER    SCOPE
944ef4844000   bridge              bridge    local
98c20fb70668   host                host      local
cd506c3a80ef   none                null      local
```

# 用户定义网络
**用户定义bridge网络**

'用户定义bridge网络' 使用 "docker network create network_name " 来创建  --driver  默认为 “bridge”模式

各个 "用户定义bridge网络" 的容器之间相互隔离，和 "默认bridge网络" 的容器之间相互隔离

同一个"用户定义bridge网络"中的容器自带DNS可以通过 "容器名称" 或者 "容器ID" 来通信

**用户定义macvlan网络**

我们使用 "默认macvlan网络" 时，容器默认不会获取dhcp服务，需要我们手动指定ip地址,macvlan有'用户定义bridge网络' 中的DNS功能。

macvlan网络会给每个容器一个独立的mac地址，一般我们的网络无法同时支持多个mac地址在同一个交换机端口上，因为容器和主机共享同一个端口，而交换机则会看到多个mac地址，如果我们想解决这个问题，我们需要开启 "混杂模式" 使用命令  "sudo ip link set eth0 promisc on" 开启，如果有物理设备或者虚拟机软件，也需要开启 "混杂模式"。

说完上面的 "默认macvlan网络" 其实macvlan还有一种模式，那就是802.11子接口模式，和单壁路由器非常相似，当然我们要想正常使用他需要配置中继传输。

**用户定义ipvlan网络**

和 macvlan网络不同 ipvlan网络不会为每个容器分配mac地址，但是和macvlan相同的是它们都会有一个独立的IP地址，ipvlan也有'用户定义bridge网络' 中的DNS功能。

ipvlan也有两种模式，一种l2模式一种l3模式，l2模式类似于交换机，而l3类似于路由器。

```text
#创建一个"用户定义bridge网络"
docker network create bridge1 [--driver  bridge]  #默认为bridge模式
```

```text
#创建一个"默认macvlan网络"
docker network create  macvlan1 \
-d macvlan \
--subnet 192.168.0.0/24 \
--gateway 192.168.0.1 \
-o parent=eth0
#创建完macvlan网络后，容器如何使用这个网络？
docker run -itd --rm --network network2 \
--name test --ip 192.168.0.150 busybox
#可以看到我们需要制定一个ip地址，注意这个ip不能和我们物理环境下的ip相互冲突
#并且我们需要开启  "混杂模式"  使用命令  "sudo ip link set eth0 promisc on"

#创建一个"802.11模式macvlan网络"
docker network create macvlan802 \
-d macvlan \
--subnet 192.168.10.0/24 \
--gateway 192.168.10.1 \
-o parent=eth0.10
#802.11子接口模式，和单壁路由器非常相似
```

```text
#l2网络模式
#与创建macvlan相差不多
docker network create ipvlanL2 \
-d ipvlan \
--subnet 192.168.0.0/24 \
--gateway 192.168.0.1 \
-o parent=eth0

#l3网络模式
docker network create ipvlanL3 \
-d ipvlan \
--subnet 10.0.0.0/24 \
--subnet 10.0.1.0/24 \
-o parent=eth0 -o ipvlan_mode=l3

#创建用于测试的容器
docker run -itd --rm --network ipvlanL3 --name test --ip 10.0.0.10 busybox
docker run -itd --rm --network ipvlanL3 --name test1 --ip 10.0.0.20 busybox
docker run -itd --rm --network ipvlanL3 --name test3 --ip 10.0.1.10 busybox
docker run -itd --rm --network ipvlanL3 --name test2 --ip 10.0.1.20 busybox
#现在他们是工作在第三层的网络，它们之间能相互路由到对方，但是无法于主机或者其它网络的容器通信
#原因是我们物理路由器的路由表中没有返回到容器的地址
#我们可以通过在物理路由器上面设置静态路由的方式使得他们能够访问外界
#目标网络为10.0.0.0/24网段时走 "host_ip" 即可。
```

## 相关笔记
- [[Docker搭建]]
- [[Docker-compose部署]]
- [[MOC-Docker]]
