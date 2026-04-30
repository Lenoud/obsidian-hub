# Firefox 浏览器容器

基于 Docker 的 Firefox 浏览器，通过 VNC 或 Web 界面远程访问，适用于自动化测试和远程浏览场景。

## 部署

```yaml
version: '3.0'
services:
  firefox:
    container_name: firefox
    hostname: firefox
    shm_size: 3G
    image: jlesage/firefox:v23.11.3
    restart: always
    ports:
      - 5900:5900
      - 5800:5800
    cpus: 3
    mem_limit: 3G
    environment:
      TZ: Asia/Hong_Kong
      LANG: zh_CN.UTF-8
      DISPLAY_WIDTH: 1920
      DISPLAY_HEIGHT: 1080
      KEEP_APP_RUNNING: 1
      ENABLE_CJK_FONT: 1
      SECURE_CONNECTION: 1
      VNC_PASSWORD: "lb781023"
    volumes:
      - ./firefox-data:/config
      - ./language:/usr/share/fonts/
```

## 说明

- Web 访问地址：`http://<IP>:5800`
- VNC 访问端口：`5900`
- `VNC_PASSWORD`：VNC 连接密码
- `DISPLAY_WIDTH` / `DISPLAY_HEIGHT`：显示分辨率 `1920x1080`
- `ENABLE_CJK_FONT`：启用中文字体支持
- `SECURE_CONNECTION`：启用安全连接
- `KEEP_APP_RUNNING`：保持应用持续运行
- 资源限制：3 核 CPU、3G 内存、3G 共享内存
- 数据持久化：`./firefox-data` 映射到 `/config`
- 自定义字体：`./language` 映射到 `/usr/share/fonts/`
