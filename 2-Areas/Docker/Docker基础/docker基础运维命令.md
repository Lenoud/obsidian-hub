# docker基础运维命令

docker基础运维命令相关技术笔记。

## 说明

以下为常用的 Docker 基础运维命令及其说明。

## 运行容器

```bash
docker run -it --name <容器名> <镜像>   # -it 一般搭配使用，意为启动命令行界面
```

## 拉取镜像

```bash
docker pull <软件包名>:<版本号>   # 镜像仓库拉取镜像，不写版本号默认为 latest
```

## 搜索镜像

```bash
docker search --limit 5 <软件包名>   # 只显示前五个镜像
```

## 查看本地镜像

```bash
docker images   # 查看本地镜像
```

## 查看运行中的容器

```bash
docker ps        # 查看当前运行的容器
docker ps -a     # 查看所有容器（包括已停止的）
```

## 停止容器

```bash
docker stop <容器id 或 容器名>   # 关闭容器
```

## 启动容器

```bash
docker start <容器id 或 容器名>   # 开启容器
```

## 进入容器（exec）

```bash
docker exec -it <容器id> /bin/bash   # 进入后台运行的容器（exit 不会关闭容器）
```

## 进入容器（attach）

```bash
docker attach <容器id>   # 进入容器，exit 退出会关闭容器
```

## 删除容器

```bash
docker rm -f <容器id 或 容器名>   # -f 强制删除正在运行的容器
```

## 查看容器日志

```bash
docker logs <容器id>   # 查看容器日志
```

## 复制容器文件到主机

```bash
docker cp <容器id>:<容器路径> <host目录>   # 将容器内的文件复制到主机（备份）
```

## 导出容器

```bash
docker export <容器id> > <新名字>.tar   # 导出当前容器到当前目录（打包成备份包），对应 import
```

## 导入容器

```bash
cat <新名字>.tar | docker import -
```
