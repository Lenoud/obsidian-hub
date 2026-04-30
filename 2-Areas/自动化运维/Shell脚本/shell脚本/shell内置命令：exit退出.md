# shell内置命令：exit退出

Shell 内置命令 exit 用于退出脚本并返回状态码。

### 作用
结束当前的shell进程。

可以返回不同的状态码，用于不同的业务处理。

exit用户退出当前shell环境进程结束运行，并且可以返回一个状态码，一般使用'$?'可以获取退出状态码

### 语法
#### 正确退出语法
```shell
exit  #默认返回状态码0，一般代表执行成功
```

#### 错误退出语法
```shell
exit 非0数字 #数字建议的范围0-255，一般代表命令执行失败
```

### exit应用场景
1. 结束当前shell进程
2. 当shell经常执行出错退出时，可以返回不同的状态值代表不同的错误

> 比如执行一个脚本里面操作一个文件时，可以返回1表示文件不存在，2表示文件没有读取权限，3表示文件类型不对。
>

### 示例
```shell
[root@ansible shell]# cat exit.sh
#!/bin/bash
echo "hello"
exit 2
echo "world"

[root@ansible shell]# bash exit.sh
hello
[root@ansible shell]# echo $?
2

```
