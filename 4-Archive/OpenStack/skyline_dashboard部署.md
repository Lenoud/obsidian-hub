# skyline_dashboard部署

skyline_dashboard部署相关技术笔记。

```yaml
#创建skyline数据库
CREATE DATABASE skyline DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
#创建用户
GRANT ALL PRIVILEGES ON skyline.* TO 'skyline'@'localhost'    IDENTIFIED BY '000000';
GRANT ALL PRIVILEGES ON skyline.* TO 'skyline'@'%'    IDENTIFIED BY '000000';

#拉取镜像skyline
docker pull 99cloud/skyline:latest
#创建文件夹
mkdir -p /etc/skyline /var/log/skyline /var/lib/skyline /var/log/nginx

#编辑配置文件
vi /etc/skyline/skyline.yaml
default:
  database_url: mysql://skyline:000000@127.0.0.1:3306/skyline
 # prometheus_endpoint: http://172.16.102.200:9091
openstack:
  keystone_url: http://192.168.200.21:5000/v3
  #控制节点keystone认证地址
  default_region: RegionOne
  interface_type: internal
  system_project_domain: demo
  system_project: service
  system_user_domain: demo
  system_user_name: skyline
  system_user_password: '000000'

#引导服务器,初始化文件
docker run -d --name skyline_bootstrap \
-e KOLLA_BOOTSTRAP="" \
-v /etc/skyline/skyline.yaml:/etc/skyline/skyline.yaml \
-v /var/log:/var/log \
--net=host 99cloud/skyline:latest

#清理引导服务器
doker rm skyline_bootstrap

#运行skyline访问8080端口
docker run -d --name skyline --restart=always \
-v /etc/skyline/skyline.yaml:/etc/skyline/skyline.yaml \
-v /var/log:/var/log \
--net=host 99cloud/skyline:latest
```

```yaml
version: '3'
services:
  skyline:
    image: 99cloud/skyline:latest
    container_name: skyline
    restart: always
    volumes:
      - /var/log/skyline:/var/log/skyline
      - /etc/skyline/skyline.yaml:/etc/skyline/skyline.yaml
      - /tmp/skyline:/tmp
    network_mode: host
```
