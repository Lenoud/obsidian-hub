# Shell 流程控制：if else 语句

Shell 流程控制 if-else 条件语句。

本文介绍 Shell 中 if、if-else、if-elif-else 三种条件判断语句的语法格式，包括多行写法和一行写法。

## if语法
```bash
#多行写法
if 条件
then
 命令
fi

#一行写法
if 表达式; then 命令; fi;
```

## if else语法
```bash
if 条件
then
 命令1
else
 命令2
fi

if 条件 ； then 命令1 ;else 命令2;fi;
```

## if elif else语法
```bash
if 条件1
then
 命令1

elif 条件2
then
 命令2

elif 条件3
then
 命令3

else
 命令4
fi
```
