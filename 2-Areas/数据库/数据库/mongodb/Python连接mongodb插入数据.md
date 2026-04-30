# Python 连接 MongoDB 插入数据

使用 Python 的 `pymongo` 库连接 MongoDB 数据库并插入数据。

## 安装 pymongo

```bash
pip install pymongo -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 连接与插入数据

```python
import pymongo

username = "root"
password = "password123"
host = "192.168.50.52"
port = 27017

myclient = pymongo.MongoClient(f'mongodb://{username}:{password}@{host}:{port}/')
mydb = myclient["runoobdb"]
mycol = mydb["sites"]

# 插入多条数据
mydict = [{"name": "RUNOOB", "alexa": "10000", "url": "https://www.runoob.com"},
          {"name": "RUNOOB2", "alexa": "10000", "url": "https://www.runoob.com"}]
x = mycol.insert_many(mydict)

# 插入单条数据
# mydict = {"name": "RUNOOB", "alexa": "10000", "url": "https://www.runoob.com"}
# x = mycol.insert_one(mydict)

print(x)
```

## 说明

| 方法 | 说明 |
| --- | --- |
| `pymongo.MongoClient()` | 创建 MongoDB 连接 |
| `myclient["db_name"]` | 选中或创建数据库 |
| `mydb["col_name"]` | 选中或创建集合 |
| `insert_many()` | 插入多条数据（列表形式） |
| `insert_one()` | 插入单条数据（字典形式） |
