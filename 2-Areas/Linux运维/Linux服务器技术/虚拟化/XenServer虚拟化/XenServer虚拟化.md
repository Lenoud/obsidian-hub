# XenServer虚拟化

Xen虚拟化架构

![[1704353130370-d413a658-f035-4b0d-8f13-c2b168ae757d.jpeg]]

XEN的虚拟化架构示意图

XEN最初是剑桥大学Xensource的一个开源研究项目，2003年9月发布了首个版本XEN 1.0，2007年Xensource被Citrix公司收购，开源XEN转由www.xen.org继续推进，该组织成员包括个人和公司(如 Citrix、Oracle等)。该组织在2011年3月发布了版本XEN 4.1。

相对于ESX和Hyper-V来说，XEN支持更广泛的CPU架构，前两者只支持CISC的X86/X86_64 CPU架构，XEN除此之外还支持RISC CPU架构，如IA64、ARM等。

XEN的Hypervisor是服务器经过BIOS启动之后载入的首个程序，然后启动一个具有特定权限的虚拟机，称之为Domain 0(简称Dom 0)。Dom 0的操作系统可以是Linux或Unix，Domain 0实现对Hypervisor控制和管理功能。在所承载的虚拟机中，Dom 0是唯一可以直接访问物理硬件(如存储和网卡)的虚拟机，它通过本身加载的物理驱动，为其它虚拟机(Domain U，简称DomU)提供访问存储和网卡的桥梁。

XEN支持两种类型的虚拟机，一类是半虚拟化(PV，Paravirtualization)，另一类是全虚拟化(XEN称其为 HVM，Hardware Virtual Machine)。半虚拟化需要特定内核的操作系统，如基于Linux paravirt_ops(Linux内核的一套编译选项)框架的Linux内核，而Windows操作系统由于其封闭性则不能被XEN的半虚拟化所支持，XEN的半虚拟化有个特别之处就是不要求CPU具备硬件辅助虚拟化，这非常适用于2007年之前的旧服务器虚拟化改造。全虚拟化支持原生的操作系统， 特别是针对Windows这类操作系统，XEN的全虚拟化要求CPU具备硬件辅助虚拟化，它修改的Qemu仿真所有硬件，包括：BIOS、IDE控制器、 VGA显示卡、USB控制器和网卡等。为了提升I/O性能，全虚拟化特别针对磁盘和网卡采用半虚拟化设备来代替仿真设备，这些设备驱动称之为PV on HVM，为了使PV on HVM有最佳性能。CPU应具备MMU硬件辅助虚拟化。

XEN的Hypervisor层非常薄，少于15万行的代码量，不包含任何物理设备驱动，这一点与Hyper-V是非常类似的，物理设备的驱动均是驻留在Dom 0中，可以重用现有的Linux设备驱动程序。因此，XEN对硬件兼容性也是非常广泛的，Linux支持的，它就支持。
