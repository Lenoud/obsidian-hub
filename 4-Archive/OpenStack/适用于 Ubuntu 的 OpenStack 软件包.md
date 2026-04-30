# 适用于 Ubuntu 的 OpenStack 软件包

适用于 Ubuntu 的 OpenStack 软件包相关技术笔记。

Ubuntu 在每个 Ubuntu 版本中都发布了 OpenStack。Ubuntu LTS 发布 每两年提供一次。来自临时版本的 OpenStack 软件包 Ubuntu 通过 Ubuntu Cloud 提供给以前的 Ubuntu LTS 档案。

注意

此处描述的归档启用需要在所有节点上完成 运行OpenStack服务。

## 归档支持
**OpenStack 2023.1 Antelope for Ubuntu 22.04 LTS：**

# add-apt-repository cloud-archive:antelope

**OpenStack Zed for Ubuntu 22.04 LTS：**

# add-apt-repository cloud-archive:zed

**OpenStack Yoga for Ubuntu 22.04 LTS：**

OpenStack Yoga is available by default using Ubuntu 22.04 LTS.

**OpenStack Yoga for Ubuntu 20.04 LTS：**

# add-apt-repository cloud-archive:yoga

**OpenStack Xena for Ubuntu 20.04 LTS：**

# add-apt-repository cloud-archive:xena

**OpenStack Wallaby for Ubuntu 20.04 LTS：**

# add-apt-repository cloud-archive:wallaby

**OpenStack Victoria for Ubuntu 20.04 LTS：**

# add-apt-repository cloud-archive:victoria

**OpenStack Ussuri for Ubuntu 20.04 LTS：**

OpenStack Ussuri is available by default using Ubuntu 20.04 LTS.

**OpenStack Ussuri for Ubuntu 18.04 LTS：**

# add-apt-repository cloud-archive:ussuri

**OpenStack Train for Ubuntu 18.04 LTS：**

# add-apt-repository cloud-archive:train

**OpenStack Stein for Ubuntu 18.04 LTS：**

# add-apt-repository cloud-archive:stein

**OpenStack Rocky for Ubuntu 18.04 LTS：**

# add-apt-repository cloud-archive:rocky

**OpenStack Queens for Ubuntu 18.04 LTS：**

OpenStack Queens is available by default using Ubuntu 18.04 LTS.

注意

有关支持的 Ubuntu OpenStack 版本的完整列表， 请参阅 [https://www.ubuntu.com/about/release-cycle](https://www.ubuntu.com/about/release-cycle) 的“Ubuntu OpenStack发布周期”。

## 示例安装
# apt install nova-compute

## 客户端安装
# apt install python3-openstackclient
