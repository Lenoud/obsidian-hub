# Shell 运算符：比较运算符

Shell 比较运算符的使用方法。

本文介绍 Shell 中的比较运算符，包括整数比较运算符、字符串/小数/整数比较运算符，以及 [[ ]] 和 [ ] 的区别，建议使用 [[ ]] 双中括号执行比较运算。

> 直接说结论，建议使用 [[ ]] 双中括号执行比较运算

## 整数比较运算符
> 只能比较整数

### 语法规则
| 运算符 | 说明 | 用法 |
| --- | --- | --- |
| -eq | equals 等于 true=0   false=1 | [ 1 -eq 2 ] |
| -ne | not equals  不等于 |  |
| -gt | great  than 大于 |  |
| -lt | lower than 小于  |  |
| -ge | greater  equals 大于等于 |  |
| -le | lower equals 小于等于 |  |
| < |  | (( 1 < 2 )) |
| <= |  | (( 1 <= 2 )) |
| > |  | (( 1 > 2 )) |
| >= |  | (( 1 >= 2 )) |
| == |  | (( 1 == 2 )) |
| != |  | (( 1 != 2 )) |

### 示例
```bash
#!/bin/bash
# 定义变量 a 和 b，分别通过用户输入赋值
read -p "输入a: " a
read -p "输入b: " b

# 判断 a 是否等于 b
if [ ${a} -eq ${b} ]; then
  echo "a 等于 b：真"
else
  echo "a 等于 b：假"
fi

# 判断 a 是否不等于 b
if [ ${a} -ne ${b} ]; then
  echo "a 不等于 b：真"
else
  echo "a 不等于 b：假"
fi

# 判断 a 是否大于 b
if [ ${a} -gt ${b} ]; then
  echo "a 大于 b：真"
else
  echo "a 大于 b：假"
fi

# 判断 a 是否小于 b
if [ ${a} -lt ${b} ]; then
  echo "a 小于 b：真"
else
  echo "a 小于 b：假"
fi

# 判断 a 是否大于等于 b
if [ ${a} -ge ${b} ]; then
  echo "a 大于或等于 b：真"
else
  echo "a 大于或等于 b：假"
fi

# 判断 a 是否小于等于 b
if [ ${a} -le ${b} ]; then
  echo "a 小于或等于 b：真"
else
  echo "a 小于或等于 b：假"
fi

# 使用双括号方式判断 a 是否大于 b
if (( ${a} > ${b} )); then
  echo "a > b：真"
else
  echo "a > b：假"
fi

# 使用双括号方式判断 a 是否小于 b
if (( ${a} < ${b} )); then
  echo "a < b：真"
else
  echo "a < b：假"
fi

# 使用双括号方式判断 a 是否等于 b
if (( ${a} == ${b} )); then
  echo "a = b：真"
else
  echo "a = b：假"
fi

# 使用双括号方式判断 a 是否不等于 b
if (( ${a} != ${b} )); then
  echo "a != b：真"
else
  echo "a != b：假"
fi

# 使用双括号方式判断 a 是否大于等于 b
if (( ${a} >= ${b} )); then
  echo "a >= b：真"
else
  echo "a >= b：假"
fi

# 使用双括号方式判断 a 是否小于等于 b
if (( ${a} <= ${b} )); then
  echo "a <= b：真"
else
  echo "a <= b：假"
fi
```

## 字符串-小数-整数 比较运算符
> 可以比较2个变量，变量的类型可以为数字（整数，小数）与字符串

### 语法规则
下表列出了常用的字符串运算符，假定变量a为"abc"，变量b为"efg"：

字符串比较可以使用  [[ ]] 和 [ ] 2种方式

| 运算符 | 说明 | 用法 |
| --- | --- | --- |
| == or = | 相等。用于比较两个字符串或数字，相同则返回0。可以使用= | [ ${a} = ${b} ] 返回 1 [ ${a} == ${b} ] [[ ${a} = ${b} ]] [[ ${a} == ${b} ]] |
| != | 不相等。用于比较两个字符串或数字，不相同则返回0 | [ ${a} != ${b} ] [[ ${a} != ${b} ]] |
| < | 小于，用于比较两个字符串或数字，小于返回0，否则返回1 | [ ${a} \< ${b} ] 单括号需要转义 [[ ${a} > ${b} ]] |
| > | 大于，用于比较两个字符串或数字，大于返回0，否则返回1 | [ ${a} \> ${b} ] 单括号需要转义 [[ ${a} > ${b} ]] |
| -z | (size) 检测字符串长度是否为0，为0则返回0，否则返回1 | [ -z $a ]  |
| -n | (null) 检测字符串长度是否不为0，不为0则返回0，否则返回1 | [ -n $a ] |
| $ | 检测字符串是否不为空，空返回1，不为空0 | [ $a ] |

> 字符串比较没有<=可以通过[[ "a" < "b" && "a" == "b" ]]

## [[ ]]  和 [ ] 的区别

### 区别1:word splitting的发生
[[ ]] 不会有word splitting发生

[ ]会有word splitting发生

### 什么是word splitting
会将有空格的字符串进行拆分后比较

```bash
[root@ansible shell]# a="abc"
[root@ansible shell]# b="abc efg"
[root@ansible shell]# [ ${a} = ${b} ]
-bash: [: too many arguments
#提示参数过多

[root@ansible shell]# [[ ${a} = ${b} ]]
[root@ansible shell]# echo $?
1
# a=b不成立
```

### 区别2:转义字符
[ ]大于小于符号需要转义而 [[ ]] 不需要转义。
