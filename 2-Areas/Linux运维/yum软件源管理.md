# yum软件源管理

yum软件源管理相关技术笔记。

## 一．实训概要
了解Yum源的管理和配置作用，掌握本地Yum源的创建与测试方法。

---

## 二．实训内容
1. **删除系统默认的Yum源：** 删除CentOS自带的网络Yum源配置文件。
2. **挂载镜像：** 创建挂载点`/mnt/centos`并挂载CentOS镜像文件。
3. **配置本地Yum源：** 创建并编写本地Yum源配置文件，保存并退出。
4. **管理缓存：** 清除Yum缓存、生成新的缓存，并列出当前已配置和启用的Yum仓库及其状态。
5. **测试Yum源：** 下载并安装`httpd`软件包，验证是否安装成功。

---

## 三．实训过程
### 1. 删除系统默认的Yum源
**目标：** 删除CentOS系统自带的网络Yum源。
**操作步骤：**

1. 进入`/etc/yum.repos.d`目录：

```bash
cd /etc/yum.repos.d
```

2. 删除所有默认的`.repo`文件：

```bash
rm -f *.repo
```

### 2. 挂载镜像文件
**目标：** 挂载CentOS镜像到指定挂载点`/mnt/centos`。
**操作步骤：**

1. 创建挂载点：

```bash
mkdir -p /mnt/centos
```

2. 挂载ISO镜像（假设镜像路径为`/root/CentOS.iso`）：

```bash
mount -o loop /root/CentOS.iso /mnt/centos
```

### 3. 创建并配置本地Yum源
**目标：** 创建并编写本地Yum源配置文件。
**操作步骤：**

1. 进入`/etc/yum.repos.d`目录：

```bash
cd /etc/yum.repos.d
```

2. 创建本地Yum源配置文件：

```bash
vi local.repo
```

3. 在文件中添加以下内容：

```bash
[local]
name=Local Repository
baseurl=file:///mnt/centos
enabled=1
gpgcheck=0
```

4. 保存并退出（`wq`）。

### 4. 管理Yum缓存
**目标：** 清除旧缓存、生成新缓存并列出仓库状态。
**操作步骤：**

1. 清除缓存：

```bash
yum clean all
```

2. 生成新的缓存：

```bash
yum makecache
```

3. 列出已配置和启用的Yum仓库：

```bash
yum repolist
```

### 5. 测试Yum源
**目标：** 下载并安装`httpd`测试本地Yum源是否工作正常。
**操作步骤：**

1. 安装`httpd`软件包：

```bash
yum install -y httpd
```

2. 验证安装是否成功：

```bash
httpd -v
```

---

## 四．验证与总结
1. **验证Yum源配置：**
    - 检查`yum repolist`输出是否显示本地Yum源状态正常。
    - 测试软件安装是否成功完成。
2. **总结：**
    - 通过本次实训，熟练掌握了Yum源的配置和管理方法，包括删除默认源、挂载镜像、配置本地源，以及通过软件包测试验证Yum源配置是否正确。
