# Python 字符与 ASCII 码互转

使用 `ord()` 和 `chr()` 函数在字符和 ASCII 码之间转换。

## chr() — 数字转字符

```python
# 十六进制
print(chr(0x30), chr(0x31), chr(0x61))  # 0 1 a

# 十进制
print(chr(48), chr(49), chr(97))  # 0 1 a
```

## ord() — 字符转数字

```python
ord('a')  # 97
ord('b')  # 98
ord('c')  # 99
```

## 说明

| 函数 | 说明 |
| --- | --- |
| `chr(i)` | 将整数（十进制或十六进制）转为对应 ASCII 字符 |
| `ord(c)` | 将字符转为对应的十进制 ASCII 码 |
