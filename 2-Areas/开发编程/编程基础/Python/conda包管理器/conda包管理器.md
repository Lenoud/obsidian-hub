# conda包管理器

[anaconda | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)

要创建一个新的Python环境，您可以运行以下命令：

```bash
conda create --name myenv python=3.8
#指定路径
conda create --prefix=D:\Environment\conda\ip_info_tool python=3.8
#环境的目录添加到 envs_dirs 配置选项中
conda config --append envs_dirs D:\Environment\conda

#添加源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple/
conda config --show-sources
```

这将创建一个名为"myenv"的新环境，并在该环境中安装Python 3.8。

要激活环境，可以运行：

```text
conda activate myenv
```

然后，您可以使用conda install命令安装所需的软件包，例如：

```bash
conda install numpy

#命令的含义是在 conda-forge 渠道中搜索并安装名为 OpenStackSDK 的软件包
conda install -c conda-forge openstacksdk
```

这将在当前激活的环境中安装NumPy包。

当您不再需要某个环境时，可以使用以下命令进行删除：

```text
conda remove --name myenv --all
```

您可以使用以下命令列出当前系统中存在的所有Conda环境：

```text
conda env list
```

这将输出所有已创建的环境列表，其中包括环境名称以及它们所在的路径。 例如：

```text
# conda environments:
#
base                  /opt/anaconda3
myenv1              /opt/anaconda3/envs/myenv1
myenv2              /opt/anaconda3/envs/myenv2
```

这里展示了3个Conda环境，分别为"base"、"myenv1"和"myenv2"。

您也可以使用以下命令查看当前激活的环境：

```text
conda info --envs
```

这将列出当前激活环境的信息，并用星号(*)标记出来。

如果您只希望列出已安装的环境中的包及其版本，可以使用以下命令：

```text
conda list
```

这将列出当前激活环境中已安装的所有包及其版本。

如果要列出特定环境中安装的包及其版本，可以在conda list命令后添加环境名称，例如：

```text
conda list -n myenv1
```

这将列出名为"myenv1"的环境中已安装的所有包及其版本。

输入以下命令来移除环境名称前面的(base)：

```bash
conda config --set changeps1 False #这将禁用在命令行中显示(base)前缀
```
