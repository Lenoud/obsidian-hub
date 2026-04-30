# 字典(dict)

字典的键是不可改变的，所以元组可以作为字典的键值，而列表却不能

## zip将两个可迭代的对象打包
```python
list1 = [x for x in range(1, 10, 2)]
list2 = [x for x in range(10, 20, 2)]
a = zip(list1, list2)
for item in a:
    print(item)
"""
(1, 10)
(3, 12)
(5, 14)
(7, 16)
(9, 18)
"""
```

## 打包后可以转换成字典格式
```python
list1 = [x for x in range(1, 10, 2)]
list2 = [x for x in range(10, 20, 2)]
print(list1, list2)
print(type(list1), type(list2))
tuple1 = zip(list1, list2)
print(dict(tuple1))
"""
[1, 3, 5, 7, 9] [10, 12, 14, 16, 18]
<class 'list'> <class 'list'>
{1: 10, 3: 12, 5: 14, 7: 16, 9: 18}

"""
dict1={k: v for k, v in zip(list1, list2) }
print(dict1)
"""
{1: 10, 3: 12, 5: 14, 7: 16, 9: 18}
"""
```

## 获取键的值（get方法）
```python
# coding: utf-8
dict1={"语文":100,"数学":98,"英语":79}
print(dict1["语文"])

print(dict1.get("语文"),dict1.get("python"),dict1.get("计算机","没有找到课程"))
"""
100
100 None 没有找到课程
"""
```

## 三个方法获取字典的键和值
```python
dict1_items = dict1.items()
print(dict1_items)
dict1_key = dict1.keys()
dict1_value = dict1.values()
print(dict1_key)
print(dict1_value)
"""
{1: 10, 3: 12, 5: 14, 7: 16, 9: 18}
dict_items([(1, 10), (3, 12), (5, 14), (7, 16), (9, 18)])
dict_keys([1, 3, 5, 7, 9])
dict_values([10, 12, 14, 16, 18])
"""

dict1={"语文":100,"数学":98,"英语":79}
for k,v in dict1.items():
    print(k,v)
"""
语文 100
数学 98
英语 79
"""
```
