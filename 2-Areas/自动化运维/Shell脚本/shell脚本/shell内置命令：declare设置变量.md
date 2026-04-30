# Shell 内置命令：declare 设置变量

Shell 内置命令 declare 用于声明变量类型。

本文介绍 Shell 中 declare 命令的用法，用于设置变量属性（如整型、只读、环境变量等）以及声明关联数组（键值对数组）。

## 设置变量属性

### 语法格式
```bash
declare [-/+] [aArxif] value_name=value

declare -i liubiao=100
#设置成整形变量

declare +i liubiao
#取消整形变量
```

### 参数说明
![[1733125329229-fd0d70c9-b714-4250-ac88-e693a9d88cdf.png]]

## 实现key-value关联数组变量语法
关联数组也称为"键值对（key/value）"数组，键（key）也即字符串形式的数组下标，值（value）也即元素值。

```bash
declare -A 关联数组名=([string_key1]=value [string_key2]=value ....)

declare -a list=([2]=value [5]=value ....)
declare -a list=(value1 value2 ....)
```

> 普通数组的定义使用 "-a" 即可，定义方式与基础的定义数组相似不再赘述
