# Shell 流程控制：while

Shell 流程控制 while 循环语句。

本文介绍 Shell 中 while 循环语句的语法和用法，包括多行写法、单行写法以及循环控制命令（continue、break）。

while用于循环执行一系列命令

## 语法
多行写法

```bash
 while 条件
 do
  命令1
  命令2
  ...
  continue;
  break;
done
```

单行写法

```bash
while 条件; do 命令; done;
```

### 循环控制
```bash
continue;  跳出当次循环
break;	跳出本层循环
```

## 示例
```bash
#!/bin/bash
read -p "输入一个数字：" number

i=0
while test ${i} -lt ${number}
do
 echo "hello ${i}"
 ((i=${i}+1))
done
```
