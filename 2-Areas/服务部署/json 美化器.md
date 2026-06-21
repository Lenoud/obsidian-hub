# json 美化器

json 美化器相关技术笔记。

```yaml
services:
  jsonhero:
    container_name: jsonhero
    image: henryclw/jsonhero-web:latest
    restart: always
    network_mode: bridge
    ports:
      - 8787:8787
    labels:
      createdBy: "Apps"
```
