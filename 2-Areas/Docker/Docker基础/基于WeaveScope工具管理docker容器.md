# 基于WeaveScope工具管理docker容器

基于WeaveScope工具管理docker容器相关技术笔记。

```shell
#从github下载软件
sudo curl -L https://github.com/weaveworks/scope/releases/download/latest_release/scope -o /usr/local/bin/scope
#赋予可执行权限
sudo chmod a+x /usr/local/bin/scope
#直接部署服务
scope launch
#部署需要登陆的服务
scope launch -app.basicAuth -app.basicAuth.password 123456 -app.basicAuth.username admin /
-probe.basicAuth -probe.basicAuth.password 123456 -probe.basicAuth.username admin
#浏览器访问IP:4040
```
