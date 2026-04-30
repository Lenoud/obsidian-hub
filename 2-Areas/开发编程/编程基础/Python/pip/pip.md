# 更换源
```bash
pip3 install wheel -i https://mirrors.aliyun.com/pypi/simple/
pip install  wheel --index-url=https://pypi.tuna.tsinghua.edu.cn/simple
```

1. 打开 pip 的配置文件 pip.ini 或 .pip/pip.conf。在 Windows 上一般在 C:\Users\你的用户名\pip 目录下，Linux 和 macOS 上一般在 ~/.config/pip/ 目录下。如果找不到该文件，可以自行创建。
2. 将以下内容添加到配置文件中：

```plain
[global]
index-url = 替换成你选择的国内镜像源地址
trusted-host = 替换成你选择的镜像源主机域名
```

其中 index-url 是指向镜像源的 URL，trusted-host 是指向镜像源的主机域名。

如果想使用清华大学的 PyPI 镜像源，配置文件应该如下所示：

```plain
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/
trusted-host = pypi.tuna.tsinghua.edu.cn
```

如果想使用阿里云的 PyPI 镜像源，配置文件应该如下所示：

```plain
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
trusted-host = mirrors.aliyun.com
```
