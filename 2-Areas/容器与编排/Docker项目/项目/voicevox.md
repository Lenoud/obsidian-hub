# VOICEVOX 语音合成引擎

开源的日语语音合成引擎，提供基于 CPU 的 TTS（Text-to-Speech）API 服务。

## 部署

```yaml
version: "3"
services:
  voicevox:
    image: aoirint/voicevox_engine:cpu-ubuntu20.04-latest
    ports:
      - 50021:50021
    container_name: voicevox
    network_mode: bridge
    hostname: voicevox
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
networks: {}
```

## 说明

- API 访问地址：`http://<IP>:50021`
- 端口映射：`50021` -> `50021`
- 使用 CPU 版本镜像
