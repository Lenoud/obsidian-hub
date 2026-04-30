# 网络 API 接口调用示例

使用 curl 和 Python 调用网络 API 接口的示例代码。

## Lolisuki API

- API 文档：https://lolisuki.cn/#/setu
- Lolicon API：https://api.lolicon.app/#/setu

### curl 调用

```bash
curl -i --request GET \
  --url 'https://lolisuki.cn/api/setu/v1?level=5&tag=自定义'
```

### Python 调用

```python
import requests

url = "https://lolisuki.cn/api/setu/v1"
querystring = {"level": "5", "tag": "自己定义"}
response = requests.request("GET", url, params=querystring)
print(response.text)
```

## 校园网认证

`-g` 参数可以避免 URL 包含括号而报错。

```bash
curl -g --request GET \
  --url 'http://10.253.0.1/drcom/login?callback=dr1003&DDDDD=账号&upass=密码&0MKKey=123456&R1=0&R2=&R3=0&R6=0&para=00&v6ip=&terminal_type=1&lang=["zh-cn","zh"]&jsVersion=4.2&v=9157'
```
