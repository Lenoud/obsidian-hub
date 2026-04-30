# Shell 内置命令：test

Shell 内置命令 test 用于条件测试。

本文介绍 Shell 中 test 命令的用法，用于检查条件是否成立，支持整数比较测试、字符串比较测试和文件测试，功能与 [ ] 相同。

> test命令能对整数比较测试
>
> test命令能对字符串比较测试
>
> test命令能对文件测试

## 说明
Shell中的test命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。

功能与`[]` 一样

## 整数比较测试

### 语法
```bash
if test 数1 options 数2
then
  命令
fi
```

### options参数：
![[1733466966608-027e9024-502c-4ebf-8ad9-c80120d06293.png]]

### 示例
```bash
[root@ansible shell]# cat test_int.sh
#!/bin/bash

if test 1 -eq 2 ;
then
 echo "1 -eq 2 = $?"
else
 echo "1 -eq 2 = $?"
fi

[root@ansible shell]# bash test_int.sh
1 -eq 2 = 1
```

---

## 字符串比较测试

![[1733467589360-14dcc26d-149a-4d0a-b9b3-3301ad4a75e9.png]]

## 文件测试
![[1733467781578-51384ad4-9f96-47bc-bf5d-227494ffaef9.png]]

## 可以搭配布尔运算符使用
注意：不可搭配 逻辑运算符 && || 这两个符号使用，因为逻辑运算符是`[[ && || ]]`中使用的

而`test`和`[]`功能一样。

```bash
-a and
-o or
! 取反

[root@ansible shell]# test 1 -eq 1 -a 2 -gt 1 ;echo $?;
0
```
