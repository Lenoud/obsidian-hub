# lsof命令(可以查看指定进程使用了那些文件)

lsof 命令查看进程打开的文件和端口。

```bash
root@luobozi:/ # lsof -p 810 (pid)  可以查看这个进程使用了那些文件
#（可以通过这种方式查应用的部署方式）
COMMAND PID USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
dockerd 810 root  cwd       DIR              252,1     4096          2 /
dockerd 810 root  rtd       DIR              252,1     4096          2 /
dockerd 810 root  txt       REG              252,1 64763136     533425 /usr/local/bin/dockerd
dockerd 810 root  mem-W     REG              252,1    32768     795919 /var/lib/docker/buildkit/cache.db
dockerd 810 root  mem-W     REG              252,1    16384     795917 /var/lib/docker/buildkit/metadata_v2.db
dockerd 810 root  mem-W     REG              252,1    16384     795916 /var/lib/docker/buildkit/snapshots.db
dockerd 810 root  mem-W     REG              252,1    16384     795914 /var/lib/docker/buildkit/containerdmeta.db
dockerd 810 root  mem-W     REG              252,1    32768     795903 /var/lib/docker/volumes/metadata.db
dockerd 810 root    0r      CHR                1,3      0t0          5 /dev/null
dockerd 810 root    1u     unix 0xffff9da75361bb80      0t0      20373 type=STREAM
dockerd 810 root    2u     unix 0xffff9da75361bb80      0t0      20373 type=STREAM
dockerd 810 root    3u     unix 0xffff9da75009eec0      0t0      20715 @0000f type=DGRAM
dockerd 810 root    4u  a_inode               0,14        0      12434 [eventpoll]
dockerd 810 root    5r     FIFO               0,13      0t0      20530 pipe
dockerd 810 root    6w     FIFO               0,13      0t0      20530 pipe
dockerd 810 root    7u     unix 0xffff9da75009f740      0t0      20730 type=DGRAM
dockerd 810 root    8u     unix 0xffff9da75009ea80      0t0      20752 /var/run/docker.sock type=STREAM
dockerd 810 root    9u     unix 0xffff9da74da05540      0t0      20970 type=STREAM
dockerd 810 root   10u     unix 0xffff9da75361aa80      0t0      21033 /var/run/docker/metrics.sock type=STREAM
dockerd 810 root   11u     unix 0xffff9da75361b740      0t0      21034 type=STREAM
dockerd 810 root   12u     unix 0xffff9da753619dc0      0t0      21038 type=STREAM
dockerd 810 root   13uW     REG              252,1    32768     795903 /var/lib/docker/volumes/metadata.db
dockerd 810 root   14r      REG                0,4        0 4026531840 net
dockerd 810 root   15u  netlink                         0t0      21279 ROUTE
dockerd 810 root   16u  netlink                         0t0      21280 XFRM
dockerd 810 root   17u  netlink                         0t0      21281 NETFILTER
dockerd 810 root   18u     unix 0xffff9da75009ddc0      0t0      21781 /var/run/docker/libnetwork/01e2c9f48c93.sock type=STREAM
dockerd 810 root   19u     FIFO               0,26      0t0       1220 /run/docker/containerd/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8/init-stdout
dockerd 810 root   20u     FIFO               0,26      0t0       1221 /run/docker/containerd/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8/init-stderr
dockerd 810 root   21w      REG              252,1     4557     925691 /var/lib/docker/containers/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8-json.log
dockerd 810 root   22r     FIFO               0,26      0t0       1220 /run/docker/containerd/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8/init-stdout
dockerd 810 root   23r     FIFO               0,26      0t0       1221 /run/docker/containerd/7851aa15f458764da660fbae2d6875b6b9d28eae4b2b7113ec9579adfa6a89a8/init-stderr
dockerd 810 root   24uW     REG              252,1    16384     795914 /var/lib/docker/buildkit/containerdmeta.db
dockerd 810 root   25uW     REG              252,1    16384     795916 /var/lib/docker/buildkit/snapshots.db
dockerd 810 root   26u     sock                0,8      0t0      22777 protocol: UDP
dockerd 810 root   27u     sock                0,8      0t0      22714 protocol: NETLINK
dockerd 810 root   28u     sock                0,8      0t0      22778 protocol: TCP
dockerd 810 root   29uW     REG              252,1    16384     795917 /var/lib/docker/buildkit/metadata_v2.db
dockerd 810 root   30uW     REG              252,1    32768     795919 /var/lib/docker/buildkit/cache.db
dockerd 810 root   32u     FIFO               0,26      0t0       2325 /run/docker/containerd/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de/init-stdout
dockerd 810 root   33u     FIFO               0,26      0t0       2326 /run/docker/containerd/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de/init-stderr
dockerd 810 root   34w      REG              252,1   183788     937992 /var/lib/docker/containers/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de-json.log
dockerd 810 root   35r     FIFO               0,26      0t0       2325 /run/docker/containerd/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de/init-stdout
dockerd 810 root   36r     FIFO               0,26      0t0       2326 /run/docker/containerd/872a1b222156c24b512f35b28d7d3e5d537ee40ce049a3623eec4618c4d5a4de/init-stderr
dockerd 810 root   39u     sock                0,8      0t0     118807 protocol: UDP
dockerd 810 root   40u     sock                0,8      0t0     118795 protocol: NETLINK
dockerd 810 root   41u     sock                0,8      0t0     118808 protocol: TCP
dockerd 810 root   42u     FIFO               0,26      0t0       3357 /run/docker/containerd/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a/init-stdin
dockerd 810 root   43u     FIFO               0,26      0t0       3358 /run/docker/containerd/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a/init-stdout
dockerd 810 root   44w      REG              252,1     2202    1074236 /var/lib/docker/containers/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a-json.log
dockerd 810 root   45w     FIFO               0,26      0t0       3357 /run/docker/containerd/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a/init-stdin
dockerd 810 root   46r     FIFO               0,26      0t0       3358 /run/docker/containerd/d911b20d220ba3b650c47cb5fda54d1c4422bd9d08a3254e90575984bb037d9a/init-stdout
dockerd 810 root   50u     sock                0,8      0t0     825222 protocol: NETLINK
```

lsof -i:80 查看端口被哪个进程占用
