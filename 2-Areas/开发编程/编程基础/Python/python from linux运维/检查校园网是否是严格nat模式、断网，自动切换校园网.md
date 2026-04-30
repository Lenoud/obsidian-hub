# Python 检查网络并自动认证校园网

自动检测网络连通性，断网时自动重新登录校园网认证。

## 代码

```python
import requests
import time
import datetime

uid = "学号"
password = "密码"

print("{:-^40}".format("程序开始运行"))
time.sleep(10)
current_time = datetime.datetime.now()

# 获取公网 IP 检测网络连通性
url = "https://ifconfig.me/ip"
try:
    status = requests.get(url)
    ipaddr = status.text
    print("公网为{:},不需要更换".format(ipaddr))
except:
    # 网络不通，执行校园网认证
    print("公网获取失败,开始登陆")
    url = f"http://10.253.0.1/drcom/login?callback=dr1003&DDDDD={uid}&upass={password}&0MKKey=123456&R1=0&R2=&R3=0&R6=0&para=00&v6ip=&terminal_type=1&lang=zh-cn&jsVersion=4.2&v=9157&lang=zh"
    resp = requests.get(url)
    print(f"返回码{resp}")

print("当前时间：", current_time)
print("{:-^40}".format("程序运行结束"))
```
