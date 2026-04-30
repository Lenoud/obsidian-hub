# 元组(tuple)

元组中的元素值不可改变

```python
tuples=(1,100,3,4,5,-1,7)
#元组最常用的三个方法
print(
len(tuples),
max(tuples),
min(tuples)
)

"""
7 100 -1 (长度，最大值，最小值)
"""
list1 = [1, 100, 3, 4, 5, -1, 7]
tuples1=tuple(range(20,35,2))
print(tuple(list1),tuples1)
"""
#可将列表转换成元组          将range迭代的值转换成元组
(1, 100, 3, 4, 5, -1, 7) (20, 22, 24, 26, 28, 30, 32, 34)
"""
```

## 元组中的元素值不可改变
```python
tuples1 = (1, 100, 3, 4, 5, -1, 7)
tuples2 = (4, 5, 6, 2, 3, 4, 5)
#可以通过连接的方式组成一个新的元组
tuples3 = tuples1 + tuples2
print(tuples3)
"""
(1, 100, 3, 4, 5, -1, 7, 4, 5, 6, 2, 3, 4, 5)
"""
```
