# Shell 计算命令：expr 命令详解

Shell expr 命令用于表达式求值。

本文介绍 Shell 中 expr 命令的用法，包括算术运算、计算字符串长度、截取字符串、查找字符位置和正则表达式匹配等功能。

## 算术运算
> shell运算符中讲过，可以使用expr进行算术运算

```bash
[root@ansible shell]# c=`expr $a \* $b`
[root@ansible shell]# echo $c
40
```

## 字符串语法

### 计算字符串的长度
```bash
expr length 字符串
#直接返回这个字符串的长度

expr length "1234567"
7
```

### 截取字符串语法
```bash
expr substr 字符串  start end
#start从1 开始
#end从这个位置结束，包含这个位置

expr substr "abcdefg"  1 3
abc
```

### 获取第一个字符在字符串中存在的位置
```bash
expr index 被查找的字符串 要查找的字符串

expr index "987654321"  4
6
#字符串"4"在第6个位置

expr index luobozi b
4
# b字符串在第4个位置
```

### 正则表达式匹配1语法
```bash
expr match 字符串 正则表达式
expr match "luobozi" ".*b"
4

#默认是带^
```

### 正则表达式匹配2语法
```bash
expr "luobozi" : ".*b"
4
```
