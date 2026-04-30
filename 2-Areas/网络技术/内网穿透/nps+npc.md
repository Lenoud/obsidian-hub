# nps+npc

nps+npc相关技术笔记。

## docker部署
nps获取配置

```shell
yum install -y wget && wget https://img.zeruns.tech/down/conf.zip
#修改nps.conf配置
web_host=localhost
web_username=admin
web_password=1234passwd
```

nps

```yaml
version: "3.7"
services:
   nps:
     hostname: nps1
     container_name: nps1
     network_mode: host
     volumes:
     - ./conf:/conf
     image: yisier1/nps:v0.26.20
     restart: unless-stopped
     logging:
       driver: "json-file"
       options:
         max-size: "200k"

```

npc

```yaml
version: "3"
services:
  npc:
    container_name: npc
    image: ffdfgdfg/npc
    restart: always
    command: "-server=121.37.153.20:8024 -vkey=v47o5j91o652cb47 -type=tcp"
```
