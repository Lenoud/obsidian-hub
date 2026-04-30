# Python 异常处理（try / except）

使用 `try-except` 语句捕获和处理程序运行时的异常。

## 基本用法

```python
try:
    a = eval(input("请输入一个数"))
    print(a**2)
except:
    print("请输入一个数字！")
```

## 指定异常类型

```python
try:
    a = eval(input("请输入一个整数"))
    print(a**2)
except NameError:
    print("请输入一个整数！")
```

`except` 可以指定异常类型，只有匹配的异常类型才会触发对应的处理逻辑。
