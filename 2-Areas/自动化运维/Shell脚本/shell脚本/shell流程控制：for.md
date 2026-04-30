# Shell 流程控制：for

Shell 流程控制 for 循环语句。

本文介绍 Shell 中 for 循环的三种常见写法（列表循环、范围循环、C 风格循环）以及无限循环的语法和示例。

## 循环方式1

### 语法
多行写法

```bash
for var in item1 item2 item3 ....
do
 command1
 command2
 ....
done
```

单行写法

```bash
for var in item1 item2 item3 .... ;do command1; command2; done;
```

### 示例
```bash
#!/bin/bash
for i in 1 3 5 7
do
 echo "当前元素：${i}"
done

[root@ansible shell]# bash for.sh
当前元素：1
当前元素：3
当前元素：5
当前元素：7
```

## 循环方式2

### 语法
多行写法

```bash
for var in {start..end}
do
 command1
 command2
 ....
done
```

> start: 循环的起始值，必须为整数
>
> end: 循环的结束值，必须为整数

单行写法

```bash
for var in {start..end};do command1; command2; done;
```

### 示例
```bash
#!/bin/bash
for i in {1..10}
do
 echo "当前元素为：${i}"
done

[root@ansible shell]# bash for2.sh
当前元素为：1
当前元素为：2
当前元素为：3
当前元素为：4
当前元素为：5
```

## 循环方式3

### 语法
多行写法

```bash
for((i=start;i<=end;i++))
do
 command1
done
```

单行写法

```bash
for((i=start;i<=end;i++)); do command1; done;
```

### 示例
```bash
#!/bin/bash
for((i=0;i<=5;i++))
do
 echo "当前元素为：${i}"
done

[root@ansible shell]# bash for3.sh
当前元素为：0
当前元素为：1
当前元素为：2
当前元素为：3
当前元素为：4
当前元素为：5
```

## 无限循环
```bash
for((;;)); do command1; done
```
