# pal服务器

pal服务器相关技术笔记。

## docker部署server端
```yaml
version: "3.0"
services:
  palworld:
    container_name: palworld
    image: thijsvanloef/palworld-server-docker:v0.33.0
    deploy:
      resources:
        limits:
          cpus: "0"
    environment:
      ADMIN_PASSWORD: lb781023
      COMMUNITY: "false"
      MULTITHREADING: "true"
      PGID: "1000"
      PLAYERS: "32"
      PORT: "8211"
      PUBLIC_IP: ""
      PUBLIC_PORT: ""
      PUID: "1000"
      QUERY_PORT: "27015"
      RCON_ENABLED: "true"
      RCON_PORT: "25575"
      SERVER_NAME: "Default Palworld Server"
      SERVER_PASSWORD: lb781023
      UPDATE_ON_BOOT: "true"
    # network_mode: host
    ports:
     - "27015:27015/udp"
     - "8211:8211/udp"
     - "25575:25575/tcp"
    restart: always
    volumes:
      - ./data:/palworld

```

## web管理工具
```yaml
version: "3.0"
services:
  palworld-server-tool:
    container_name: palworld-server-tool
    image: jokerwho/palworld-server-tool:0.6.2
    network_mode: host
    #8080
    restart: always
    deploy:
      resources:
        limits:
          cpus: "0"
    environment:
      RCON__ADDRESS: 127.0.0.1:25575
      RCON__PASSWORD: lb781023
      SAVE__DECODE_PATH: /app/sav_cli
      SAVE__PATH: /game/Level.sav
      SAVE__SYNC_INTERVAL: "600"
      WEB__PASSWORD: lb781023
    volumes:
    - ./data/:/game
```

## 官方网址
[https://tech.palworldgame.com/](https://tech.palworldgame.com/)
