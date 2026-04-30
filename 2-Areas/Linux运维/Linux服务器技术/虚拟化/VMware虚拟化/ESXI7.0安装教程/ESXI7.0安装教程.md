# ESXI7.0安装教程

VMware ESXi 7.0 安装教程。

## 关于网卡支持：
ESXI7.0全系没有螃蟹PCIE网卡驱动，螃蟹PCIE网卡的请用6.7版本，EMMC的请选择PVE

解决ESXI 7/8无法使用8125B等螃蟹PCIE网卡的问题。

解决方法：https://www.bilibili.com/video/BV1jF411178u/    （感谢大佬，本人亲测该方法在ESXI 7/8中均可以使用），不过该方法只能把网卡直通给虚拟机，无法做其他设置。（[https://www.right.com.cn/FORUM/thread-7881507-1-1.html](https://www.right.com.cn/FORUM/thread-7881507-1-1.html)）

PS：只有8125B的设备可以插ESXI支持的网卡进行安装，如果是插的USB网卡，每次重启后会自动解除管理口绑定，需要重新插键鼠显示器重新绑定管理网卡，不然无法进入ESXI后台。可以在已经直通网卡了的虚拟机（比如爱快/op）中添加一个虚拟网卡，把虚拟网卡和直通网卡进行桥接以后就可以解决问题，缺点是虚拟机坏了或关机了就进不去ESXI后台，需要插键鼠显示器重新绑定下USB管理网卡。

## **安装esxi7时系统盘空间被占用**
ESXi7的系统分区OSData默认会占用120G左右硬盘空间，不管是HDD还是SSD，而当硬盘是SSD时，还会将此空间作为虚拟闪存，所以我们的解决方案就是调整系统的OSData分区大小。

### 解决方法
刚入安装引导时按shift+o会出现这个：

在后面空格加一段 autoPartitionOSDataSize＝8192（不一定为8192可以10240）调整osdata分区的大小

如下：

![[1684044815185-dcff0fcf-f19e-46cd-8ca7-249f255c15db.png]]

输入完以后按回车键（其余安装步骤不变）

## exsi控制台访问shell

Troubleshooting options

#开启控制台shell

#回到主页

alt+f1进入shell

alt+f2退出shell

## 许可证
```bash
JA0W8-AX216-08E19-A995H-1PHH2  VMware vSphere 7 Enterprise Plus 的许可证密钥有
JU45H-6PHD4-481T1-5C37P-1FKQ2
1U25H-DV05N-H81Y8-7LA7P-8P0N4
HV49K-8G013-H8528-P09X6-A220A
1G6DU-4LJ1K-48451-3T0X6-3G2MD
5U4TK-DML1M-M8550-XK1QP-1A052
```
