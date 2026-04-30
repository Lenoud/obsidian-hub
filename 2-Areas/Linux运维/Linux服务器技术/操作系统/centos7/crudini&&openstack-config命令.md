# 命令crudini&&openstack-config

使用 crudini 命令快速修改 INI 格式配置文件。
## 安装
官方下载地址：[Crudini Download for Linux (deb, rpm)](https://github.com/pixelb/crudini/releases)

yum -y install cru

## 介绍
crudini 是 Pádraig Brady 用 Python 开发的、用来对配置文件（即 ini 文件）进行编辑的工具。crud 是 4 个单词的首字母简写，即 create、read、update 和 delete，中文译为“增删改查”。这个是数据的最常见的 4 类操作方法。有些软件的配置文件采用的是 ini 格式，如 php.ini。

最近，在学习 OpenStack 过程中，遇到两个非常相像的命令：crudini 和 openstack-config。它们原来是 Pádraig Brady 用 Python 开发的、用来对配置文件（即 ini 文件）进行编辑的工具。它们是同一个命令，有两个名字而已。

## 使用
以下是 crudini 命令的使用方法：

crudini --set [--existing] **config_file** **section** [**param**] [**value**]
	     --get [--format=sh|ini|lines] config_file [section] [param]
	     --del [--existing] config_file section [param]
	     --merge [--existing] config_file [section]

crudini 命令是 Linux 下的一个操作配置文件的命令工具。它用于操作 ini 文件，可以设置、获取、删除、合并其中的变量。

其中：config_file 代表要操作的文件名，section 表示变量所在的部分，如以下配置文件：

section 则表示了以下配置文件中的 DEFAULT 和 URL，在命令中不需要加中括号 []，param 则如 user，passwd，client 等。

```bash
[DEFAULT]
user = admin
passwd = admin
port = 8088
[URL]
client = 127.0.0.1:8088
admin = 127.0.0.1:8080
```

```bash
crudini --set config.conf DEFAULT user liubiao
#修改一个现有的变量：
crudini --set --existing DEFAULT user demouser
#将 shell 中的变量写入配置文件：
echo name="$name" | crudini --merge config_file section
#将两个 ini 文件合并：
crudini --merge config_file < another.ini
#删除一个变量：
crudini --del config_file section parameter
#删除一个配置段落：
crudini --del config_file section
#输出一个参数值：
crudini --get config_file section parameter
#输出一个不属于任何一个配置段落的全局变量值：
crudini --get config_file '' parameter
#输出一个配置段落：
crudini --get config_file section
```

##
