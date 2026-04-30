# jieba 中文分词库

jieba 是 Python 中最常用的中文分词第三方库，主要使用 `jieba.lcut()` 函数。

## 基本用法

```python
import jieba

s = "我星期天才能坐火车回学校你什么时候回来"
print(jieba.lcut(s))
# ['我', '星期天', '才能', '坐火车', '回', '学校', '你', '什么', '时候', '回来']
```

## 说明

| 函数 | 说明 |
| --- | --- |
| `jieba.lcut(s)` | 精确模式分词，返回列表 |
| `jieba.lcut(s, cut_all=True)` | 全模式分词 |
| `jieba.lcut_for_search(s)` | 搜索引擎模式分词 |
