# Shell 函数：自定义函数

Shell 脚本自定义函数的定义与调用。

本文介绍 Shell 中自定义函数的定义语法和调用方式，包括无参无返回值、无参有返回值、有参函数三种类型的代码示例。

## Shell程序命令与函数的区别

Shell程序命令：运行命令时开启一个子进程运行命令

函数：在当前Shell环境中运行，没有开启进程

## 说明
开发人员可以通过自定义开发函数，实现代码重用

## 语法

```bash
#函数的定义
[ function ] funname()
{
  command1
  [ return 返回值 ]
}

#调用函数
funname 传递参数1 传递参数2 ....
```

1.可以带function  fun（）定义，也可以直接fun(）定义，不带任何参数。

2.参数返回，可以显示加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。

return后跟数值n（0~255)

## 无参无返回值函数
```bash
#!/bin/bash
#定义函数
demo()
{
    echo "无参无返回值函数"
}

#调用函数
demo

[root@ansible shell]# bash fun1.sh
无参无返回值函数
```

## 无参有返回值函数
```bash
#!/bin/bash
#定义函数
sum()
{
    echo "求两数之和"
    read -p "输入第一个数：" n1
    read -p "输入二一个数：" n2
    echo "求${n1}+${n2}"
    return $(($n1+$n2))
}

#调用函数
sum

echo "值为：$?"

[root@ansible shell]# bash fun2.sh
求两数之和
输入第一个数：2
输入二一个数：3
求2+3
值为：5
```

## 有参函数
```bash
#!/bin/bash
#定义函数
funPer()
{
    echo "第一个数$1"
    echo "第二个数$2"
    echo "第三个数$3"
    echo "第五个数$5"
    echo "总个数：$#"
    echo "一起输出所有值：$*"
}

#调用函数
funPer 1 2 3 4 5 6 7 8

[root@ansible shell]# bash fun3.sh
第一个数1
第二个数2
第三个数3
第五个数5
总个数：8
一起输出所有值：1 2 3 4 5 6 7 8
```
