# Shell 流程控制：until

Shell 流程控制 until 循环语句。

本文介绍 Shell 中 until 循环语句的语法和用法，until 与 while 相反，条件为 false 时持续循环，条件为 true 时停止。

> until也是循环结构语句，until循环与while循环在处理方式上刚好相反，循环条件为false会一直循环，条件true停止循环。

## 语法
```bash
until 条件
do
 命令
done
```

条件如果返回值为1(代表false)，则继续执行循环体内的语句，否则跳出循环。

## 示例
```bash
#!/bin/bash
n=6
read -p "请输入一个比${n}小的值：" number

until test $n -eq $number
do

 echo "条件返回值为 $?"
((number++))
done

echo "最后 $?"

[root@ansible shell]# bash until_ne.sh
请输入一个比6小的值：1
条件返回值为 1
条件返回值为 1
条件返回值为 1
条件返回值为 1
条件返回值为 1
最后 0
```
