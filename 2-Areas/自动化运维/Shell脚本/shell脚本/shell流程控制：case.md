# Shell 流程控制：case

Shell 流程控制 case 多分支语句。

本文介绍 Shell 中 case in 多分支条件判断语句的语法和用法，包括支持的正则表达式格式和代码示例。

了解使用case多分支条件判断

## 说明
Shell case语句为多选择语句。可以用case语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令；

当分支较多，并且判断条件比较简单时，使用casein语句就比较方便了。

## case in 和正则表达式
case in 的 pattern 部分支持简单的正则表达式，具体来说，可以使用以下几种格式：

| 格式 | 说明 |
| :--- | :--- |
| * | 表示任意字符串。 |
| [abc] | 表示 a、b、c 三个字符中的任意一个。比如，[15ZH] 表示 1、5、Z、H 四个字符中的任意一个。 |
| [m-n] | 表示从 m 到 n 的任意一个字符。比如，[0-9] 表示任意一个数字，[0-9a-zA-Z] 表示字母或数字。 |
| \| | 表示多重选择，类似逻辑运算中的或运算。比如，abc \| xyz 表示匹配字符串 "abc" 或者 "xyz"。 |

## 语法
```bash
case 值 in
匹配模式1)
  command1
  command2
  ....
  ;;

匹配模式2)
  command1
  command2
  ....
  ;;
*)
  command1
  command2
  ....
  ;;
  esac
```

## 示例
```bash
#!/bin/bash
read -p "请输入0-7之间的数字：" number

case $number in
 1)
 echo "今天是星期${number}"
 ;;
 2)
 echo "今天是星期${number}"
 ;;
 3)
 echo "今天是星期${number}"
 ;;
 4)
 echo "今天是星期${number}"
 ;;
 5)
 echo "今天是星期${number}"
 ;;
 6)
 echo "今天是星期${number}"
 ;;
 7|0)
 echo "今天是星期天"
 ;;
 *)
 echo "输入的数字无效"
 ;;
esac
```
