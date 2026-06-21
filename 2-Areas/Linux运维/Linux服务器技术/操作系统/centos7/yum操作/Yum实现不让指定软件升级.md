# Yum实现不让指定软件升级

使用 yum-plugin-versionlock 锁定软件版本防止误升级。

## 前言
使用yum update不小心把docker、mysql什么的同时升级了最坑爹了。为避免此情况，我们需要使用yum-versionlocks锁定软件版本。

## 安装
安装yum-versionlock

```text
yum -y install yum-versionlock
```

## 锁定软件
```text
yum versionlock add docker-ce* containerd.io
```

查看锁定软件列表

```ruby
$ yum versionlock list
yum versionlock list
Loaded plugins: fastestmirror, versionlock
3:docker-ce-19.03.14-3.el7.*
1:docker-ce-cli-19.03.14-3.el7.*
0:containerd.io-1.3.9-3.1.el7.*
versionlock list done
```

清除指定锁定

```bash
$ yum versionlock delete docker-ce* containerd.io
Loaded plugins: fastestmirror, versionlock
Deleting versionlock for: 3:docker-ce-19.03.14-3.el7.*
Deleting versionlock for: 1:docker-ce-cli-19.03.14-3.el7.*
Deleting versionlock for: 0:containerd.io-1.3.9-3.1.el7.*
versionlock deleted: 3
```

清除全部锁定

```text
yum versionlock clear
```
