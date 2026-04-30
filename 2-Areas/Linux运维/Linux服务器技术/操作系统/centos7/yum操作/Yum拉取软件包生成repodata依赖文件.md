# Yum拉取软件包生成repodata依赖文件

Kubernetes 容器编排相关笔记。

---

1. **安装 createrepo 工具**（如果未安装）：**createrepo** 工具用于创建 **repodata** 依赖文件，确保它已经安装。你可以使用以下命令安装它：

```bash
sudo yum install createrepo
```

2. **创建一个目录**，用于存放软件包和依赖文件：

```bash
mkdir /path/to/your/repo
cd /path/to/your/repo
```

在这个目录中，你将存放要拉取的软件包和生成的 **repodata** 文件。

3. **拉取软件包和依赖包**：使用 **yumdownloader** 命令来拉取特定软件包以及它们的依赖包。例如，要拉取一个名为 **example-package** 的软件包，可以运行以下命令：

```bash
sudo yum install yum-utils  # 如果未安装 yum-utils
yumdownloader --resolve example-package
```

这将下载 **example-package** 及其依赖关系到当前目录。

4. **生成 repodata 依赖文件**：一旦你已经下载了软件包和依赖包，可以使用 **createrepo** 工具来生成 **repodata** 依赖文件。在存放软件包的目录中，运行以下命令：

```bash
createrepo .
```

这将在当前目录中生成 **repodata** 目录，其中包含了 **repomd.xml** 等依赖文件。

现在，你已经成功拉取了特定的软件包和它们的依赖包，并生成了 **repodata** 依赖文件。你可以将这个目录用作本地的 YUM 存储库，以便在其他系统上安装软件包时使用。请注意，这个本地存储库不会自动更新，因此需要手动添加新的软件包或依赖关系，然后再次运行 **createrepo** 来更新 **repodata**。

---

```shell
#1.安装yumdownloader命令
#yumdownloader命令是yum-utils软件包的一部分，因此需要先安装该软件包。
yum install yum-utils

#命令拉取
yumdownloader httpd
#可以使用--destdir选项指定下载的软件包存放路径
#使用--resolve选项解决依赖关系并下载所需的包

#例如，以下命令将下载httpd软件包及其依赖项，并将它们存储在/tmp目录中：
yumdownloader --destdir=/tmp --resolve httpd
```

```shell
#下载yum的插件
yum install yum-plugin-downloadonly

#命令拉取
yum install --downloadonly httpd
#软件包及其依赖项将下载到默认的yum缓存目录中。
#您可以使用--downloaddir选项指定下载的软件包存放路径。

#例如，以下命令将下载httpd软件包及其依赖项，并将它们存储在/tmp目录中：
yum install --downloadonly --downloaddir=/tmp httpd

```

```shell
#createrepo 创建依赖关系表以生成软件仓库
yum -y install createrepo

#拉取vim包和依赖
yumdownloader --destdir=/root/pkg vim --resolve

#生成依赖命令
createrepo /root/pkg
#查看 会生成一个repodata依赖关系表
ls pkg

#当前目录中RPM软件包发生变化后，重做软件仓库及元数据信息：
createrepo --update /root/pkg

```

---
