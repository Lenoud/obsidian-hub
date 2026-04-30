# Python time 时间库

Python 标准库 `time` 用于时间获取、格式化和程序计时。

## 时间获取

```python
import time

time.time()        # 返回时间戳（float，从1970年1月1日起的秒数）
time.ctime()       # 返回可读时间字符串，如 "Wed Jul 26 21:33:03 2023"
time.gmtime()      # 返回 UTC 时间的 struct_time 对象
time.localtime()   # 返回本地时间的 struct_time 对象
```

## 时间格式化

```python
time.strftime(format, t)   # 将 struct_time 格式化为字符串
time.strptime(string, fmt)  # 将字符串解析为 struct_time
```

## 程序计时

```python
time.perf_counter()  # 高精度计时器
time.sleep(secs)     # 休眠指定秒数
```
