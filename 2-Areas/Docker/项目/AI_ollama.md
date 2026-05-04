# Ollama AI 大语言模型

本地运行大语言模型的工具，配合 Open WebUI 提供 ChatGPT 风格的 Web 聊天界面。

## 部署

### Daemon 服务

```yaml
services:
  ollama:
    image: ollama/ollama:0.5.7
    container_name: ollama-deamon
    restart: unless-stopped
    ports:
      - 11434:11434
    tty: true
    volumes:
      - ./data:/root/.ollama
    labels:
      createdBy: "AI"
networks:
  1panel-network:
    external: true
```

### WebUI

```yaml
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: ollama-webui
    restart: unless-stopped
    ports:
      - 8080:8080
    environment:
      - OLLAMA_BASE_URL="http://192.168.10.100:11434"
      - WEBUI_SECRET_KEY="000000"
    volumes:
      - ./data:/app/backend/data
    labels:
      createdBy: "Apps"
networks:
  1panel-network:
    external: true
```

## 说明

- Ollama API 端口：`11434`
- WebUI 访问地址：`http://<IP>:8080`
- `OLLAMA_BASE_URL`：WebUI 连接 Ollama 服务的地址
- `WEBUI_SECRET_KEY`：WebUI 密钥
- 模型数据持久化：`./data` 映射到 `/root/.ollama`（Daemon）和 `/app/backend/data`（WebUI）
- 使用外部网络 `1panel-network`
