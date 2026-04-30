# Shell 运算符：算术运算符

Shell 算术运算符（+、-、*、/、%）的使用方法。

本文介绍 Shell 中的算术运算符（加、减、乘、除、取余），使用 expr 命令进行整数算术运算的语法和示例。

![[1733127154821-ef6a8394-382e-4bbf-99bc-fed550b28da0.png]]

## 示例
```bash
#`` 反引号 esc 下方的按钮

[root@ansible shell]# c=`expr $a \* $b`
[root@ansible shell]# echo $c
40

[root@ansible shell]# c=`expr $a + $b`
[root@ansible shell]# echo $c
14

[root@ansible shell]# c=`expr \( $a + $b \) \* \( $a + $b \)`
[root@ansible shell]# echo $c
196

[root@ansible shell]# cat sum.sh
#!/bin/bash
read -p "输入a：" a
read -p "输入b：" b

echo "a=${a}  b=${b}"
echo

echo "a+b=` expr ${a} + ${b} `"
echo "a-b=` expr ${a} - ${b} `"
echo "a/b=` expr ${a} / ${b} `"
echo "a*b=` expr ${a} \* ${b} `"
echo "a%b=` expr ${a} \% ${b} `"

[root@ansible shell]# bash sum.sh
输入a：57
输入b：28
a=57  b=28

a+b=85
a-b=29
a/b=2
a*b=1596
a%b=1
```
