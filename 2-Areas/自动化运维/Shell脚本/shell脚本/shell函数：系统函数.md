# Shell 函数：系统函数

Shell 内置系统函数的使用方法。

本文介绍 Shell 中常用的系统函数 basename 和 dirname 的用法，分别用于从文件路径中提取文件名和目录名。

## 分类
> 系统函数
>
> 自定义函数

## basename系统函数

### 说明
basename函数用于获取文件名的函数，根据给出的文件路径截取出文件名

### 语法
```bash
[root@ansible shell]# basename /root/shell/for.sh
for.sh
[root@ansible shell]# basename /root/shell/for.sh .sh
for
```

## dirname系统函数

### 说明
dirname用于从指定的路径中获取目录名，去掉文件名

### 语法
```bash
[root@ansible shell]# dirname /root/shell/for.sh
/root/shell
[root@ansible shell]# dirname ./for.sh
.
```
