# Python 文件处理（open）

使用 `with open` 语句安全地读写文件，自动处理文件关闭。

## 读取文件

```python
with open('./example.txt', 'r', encoding='utf-8') as file:
    data = file.read()
    print(data)
```

## CSV 文件读写

```python
import csv

# 写入 CSV 文件
headers = ['ID', 'UserName', 'Password', 'Age', 'Country']
rows = [(1001, 'qiye', 'qiye_pass', 24, 'China'),
        (1002, 'Mary', 'Mary_pass', 20, 'USA')]

with open('qiye.csv', 'w') as f:
    f_csv = csv.writer(f)
    f_csv.writerow(headers)
    f_csv.writerows(rows)

# 读取 CSV 文件
with open('qiye.csv', 'r') as f:
    f_csv = csv.reader(f)
    headers = next(f_csv)
    print(headers)
    for row in f_csv:
        print(row)
```

## 说明

| 模式 | 说明 |
| --- | --- |
| `'r'` | 只读 |
| `'w'` | 写入（覆盖） |
| `'a'` | 追加写入 |
