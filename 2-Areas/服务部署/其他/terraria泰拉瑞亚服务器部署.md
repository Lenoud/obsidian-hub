# terraria泰拉瑞亚服务器部署

terraria泰拉瑞亚服务器部署相关技术笔记。

## 初始化世界
```bash
#对于第一次运行，您需要生成一个大小为：1 = Small， 2 = Medium， 3 = Large 的新世界
docker run -it -p 7777:7777 --rm  \
-v /home/luobozi/terraria/world:/root/.local/share/Terraria/Worlds \
-v /home/luobozi/terraria/plugins:/plugins \
ryshe/terraria:tshock-1.4.4.9-5.2.0-3 \
-world /root/.local/share/Terraria/Worlds/world_1.wld \
-autocreate 1

```

```bash
version: "3"
services:
  terraria:
    container_name: terraria
    image: ryshe/terraria:tshock-1.4.4.9-5.2.0-3
    stdin_open: true
    tty: true
    environment:
      - WORLD_FILENAME=world_1.wld
      - CONFIGPATH=config.json
    ports:
      - 7777:7777
    volumes:
      - ./world/:/root/.local/share/Terraria/Worlds
      - ./plugins:/plugins
      - ./logs/:/tshock/logs
    restart: unless-stopped
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
```

## GitHub：
[https://github.com/ryansheehan/terraria](https://github.com/ryansheehan/terraria)
