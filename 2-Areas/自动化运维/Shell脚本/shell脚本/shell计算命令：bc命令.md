# Shell 计算命令：bc 命令

Shell bc 命令用于高精度数学计算。

本文介绍 Shell 中 bc 命令的用法，bc 是一个支持浮点数运算和数学函数的计算器工具，支持交互式运算、非交互式管道运算和重定向运算三种方式。

> bc可以进行浮点数，及其它数学运算

## 互动式数学运算
```bash

bc -q
#取消欢迎提示

bc -l
#启用数学运算库

[root@ansible shell]#  bc -ql
e(2)
7.38905609893065022723
1+3
4

[root@ansible shell]# cat bc_exprssion
1+2
(3009*20+2)/20
e(2)
s(2)[root@ansible shell]# bc -ql bc_exprssion
3
3009.10000000000000000000
7.38905609893065022723
quit()
```

## 非交互式管道运算
```bash
[root@ansible shell]# echo "100*2" |bc
200

#使用数学公式
[root@ansible shell]# echo "e(2)" |bc -l
7.38905609893065022723

#输出为2进制
[root@ansible shell]# echo "obase=2;7" |bc
111
```

### 使用变量
```bash
[root@ansible shell]# a=10
[root@ansible shell]# b=20
[root@ansible shell]# echo "c=${a}*${b};c" |bc
200
```

### 将bc结果赋值给变量
```bash
# ``  反引号
[root@ansible shell]# var_name1=`echo "c=${a}*${b};c" |bc`
[root@ansible shell]# echo $var_name1
200

# $()
[root@ansible shell]# var_name2=$(echo "c=${a}*${b};c" |bc)
[root@ansible shell]# echo $var_name2
200
```

$()与 `` 功能一样，都是执行里面的命令

![[1733398559974-c0c17fda-ecc4-4a42-a0ca-6cd6fc6add37.png]]

## 非交互式重定向运算
> 将计算表达式输出给bc去执行，特点类似于文件中输入，可以输入多行表达式，更加清晰

```bash
#``反引号
[root@ansible shell]# var_name3=`bc << EOF
1+2+3
3*20/2
EOF
`
[root@ansible shell]# echo $var_name3
6 30

# $()
[root@ansible shell]# var_name4=$(bc << EOF
> 100+29
> 128+20/2
> EOF
> )

[root@ansible shell]# echo $var_name4
129 138
```
