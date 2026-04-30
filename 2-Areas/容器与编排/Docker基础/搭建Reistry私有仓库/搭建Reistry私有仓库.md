# 搭建Reistry私有仓库

搭建Reistry私有仓库相关技术笔记。

[registry 私有仓库的搭建过程_registry搭建_噫噫噫呀呀呀的博客-CSDN博客](https://blog.csdn.net/weixin_46249268/article/details/119326336)

## 一、私有库的应用场景
像Dockerhub、阿里云这样的公共镜像仓库有的时候用起来不太方便：Dockerhub网速太慢；阿里云需要花钱买云主机。

另外，涉及内部资源的保密性，有的机构不太可能将镜像提供给公网，因此需要创建一个基于内部项目镜像，构造本地私人仓库供给团队使用。

因此，Docker提供了Dcoker Registry工具，可以用于构建私有镜像仓库。

## 二、准备工作
为了实现本地镜像发布到私有仓库Registry，准备工作主要包括：

（1）首先，安装Reistry和验证安装成功；

（2）然后，创建一个本地的新镜像，用于发布到私有仓库Registry。

#### 1、下载镜像Docker Registry
Docker Registry工具也是个docker镜像，它的功能就是用于创建私服版本个人企业版的docker hub。输入如下命令下载Docker Registry：

```bash
docker pull registry
```

#### 2、运行私有库Registry的方法
通过上一步我们已经具有了Registry镜像了，这里需要run该镜像，构建一个本地私有服务器容器，并将本地镜像发送上去，命令如下所示：

```bash
docker run -d -p 5000:5000 -v /data/registry:/var/lib/registry --restart=always \
--name registry--privileged=true registry
#--privileged=true 让容器拥有真正的root权限
```

默认情况下，仓库创建在容器的/var/lib/registry目录下，也就是说每次我们上传到registry的私人服务器仓库的文件就存储到/var/lib/registry目录下。考虑到权限管理问题，在实际应用中通常需要自定义容器卷映射，以便与宿主机联调。

根据需要上传的镜像：

这里的具体代码如下所示：

```bash
docker tag centos:7 0.0.0.0:5000/centos:v1
```

这样就将centos:7镜像修改为符合私服规范的Tag了

#### 3、 修改配置文件使docker支持http
由于docker的私服库作了安全加固，通常是不支持http的推送，所以这里需要配置取消该限制，代码如下所示：

首先查看，安装的docker支持的可访问的支持http：

cat /etc/docker/daemon.json

可以看出，安装的docker只支持阿里云镜像加速网址的http连接，为了实现镜像上传到私有库Registry，我们还要配置支持本地安装私有库Registry的http连接，代码如下：

```json
{  "insecure-registries": ["0.0.0.0/0"],
  "registry-mirrors": ["https://fem5eo07.mirror.aliyuncs.com"]
}
```

修改/etc/docker/daemon.json配置文件，在终端输入sudo gedit /etc/docker/daemon.json，打开配置文件添加如下内容（注意不要遗漏,）：

在终端输入systemctl restart docker命令重启docker，配置完成。

```bash
systemctl daemon-reload
systemctl restart docker
```

#### 4、 push推送到私服库
命令格式为：

docker push  符合私服规范的Tag的镜像名称或ID:版本号

具体代码为：

```bash
docker push 172.16.0.1:5000/centos:v1
```

上传私服库完成，在终端输入命令curl -XGET [http://172.16.0.1:5000/v2/_catalog](http://0.0.0.0:5000/v2/_catalog)查看私服库上是否有推送的镜像。

```bash
curl -XGET http://172.16.0.1:5000/v2/_catalog
#	列出私有仓库的centos 镜像有哪些tag
curl http://192.168.110.10:5000/v2/centos/tags/list
```

#### 5、先删除原有的centos的镜像，再测试私有仓库下载
```bash
docker rmi -f 8652b9f0cb4c
docker pull 172.16.0.1:5000/centos:v1
```
