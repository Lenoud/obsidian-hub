# Python 自动获取 PID 并 Kill 进程

通过 Python 脚本自动获取指定服务的 PID 并强制终止进程。

## 代码

```python
import os

# 执行 systemctl status 命令并保存输出
os.system("systemctl status todeskd > /root/todesk/todeskd.status")

# 读取 status 内容
fi = open("/root/todesk/todeskd.status", "r")
data = fi.read()
fi.close()

# 查找 PID 位置
s = data.find("PID:")
pid = data[s+5:s+12]

# 提取纯数字 PID
str1 = ""
for i in pid:
    if i in "1234567890":
        str1 = str1 + i

# 执行 kill 命令
os.system(f"kill -9 {eval(str1)}")
```

## 说明

| 步骤 | 说明 |
| --- | --- |
| 获取状态 | `systemctl status` 获取服务状态 |
| 查找 PID | 字符串查找 `PID:` 关键字 |
| 终止进程 | `kill -9` 强制终止 |
