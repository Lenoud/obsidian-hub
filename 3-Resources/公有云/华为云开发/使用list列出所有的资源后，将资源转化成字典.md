# List 资源转字典

使用华为云 SDK 列出所有云硬盘资源，并将响应转化为字典格式获取指定字段。

```python
response = client.list_volumes(request)
dict = response.volumes[0].to_dict()  # 将第一个卷转化为字典格式
print(dict["id"])

if dict["name"] == "chinaskills_volume":
    print("----------------------------------")
    print("chinaskills_volume的id为", dict["id"])
```
