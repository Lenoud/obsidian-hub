# docker基础运维命令

docker基础运维命令相关技术笔记。

## 说明

以下为常用的 Docker 基础运维命令及其说明。

## 运行容器

```text
docker run -it --name (name是为容器指定的新名字)(it一般搭配使用，意为启动命令行界面)
```

## 拉取镜像

```text
docker pull 软件包名   镜像仓库拉取镜像：版本号（不写默认最新版）
```

## 搜索镜像

```text
docker seatch --limit 5 (只显示前五个镜像)软件包名  从镜像仓库查找镜像
```

## 查看本地镜像

```text
docker images 查看本地镜像
```

## 查看运行中的容器

```text
docker ps -a(看以前运行过的容器)查看当前已运行的镜像
```

## 停止容器

```text
docker stop 镜像id或镜像name  关闭
```

## 启动容器

```text
docker start 镜像id或镜像name  开启
```

## 进入容器（exec）

```text
docker exec -it 容器id /bin/bash  进入到后台运行的容器  （并且该命令使用exit不会关闭容器）
```

## 进入容器（attach）

```text
docker attach  容器id  进入容器，exit退出会正常关闭容器
```

## 删除容器

```text
docker rm -f(强制删除) 删除正在运行的容器
```

## 查看容器日志

```text
docker logs 容器id(查看容器日志)
```

## 复制容器文件到主机

```text
docker ps 容器id：容器路径   host目录下 将容器下的内容复制到主机里（备份）
```

## 导出容器

```text
docker export 容器id  > 新名字.tar  导出当前容器到当前目录（将容器整个打包成备份包）对应import
```

## 导入容器

```bash
cat 新名字.tar |  docker import Q
```
