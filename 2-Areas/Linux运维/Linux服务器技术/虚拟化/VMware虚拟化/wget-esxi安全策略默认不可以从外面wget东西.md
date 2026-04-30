# wget-esxi安全策略默认不可以从外面wget东西

解决 ESXi 默认安全策略不允许 wget 下载的问题。

![[1704959997953-9ca5438c-9187-49f5-9790-74b3b9f51a12.png]]

![[1704959998026-d9880853-ab48-49e9-ba75-81ae45785c0f.png]]

解决方案

```bash
esxcli network firewall get
esxcli network firewall unload
```
