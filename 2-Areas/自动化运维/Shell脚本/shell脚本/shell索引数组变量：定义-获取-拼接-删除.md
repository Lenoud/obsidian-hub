# shell索引数组变量：定义-获取-拼接-删除

Shell 索引数组的定义、获取、拼接和删除操作。

### 介绍
shell支持数组（array），数组是若干数据的集合，其中的每一份数据都称为数组的元素。

bash shell 只支持一维数组，不支持多维数组。

### 数组的定义
#### 语法
在shell中，用括号()来表示数组，数组元素之间用空格来分隔，语法为：

```shell
arr=(1 2 3 4 5 6 7)

arr[2]=10

arr=([2]=100 [3]=200)
```

### 数组的获取
#### 语法
通过下标获取元素值，index从0开始

```shell
${arr[index]}
```

获取值同时复制给其它变量

```shell
arr2=${arr[index]}

arr3=(${arr[*]} ${arr1[*]})
#拼接两个数组，组成一个
```

使用@或者*可以获取数组中的所有元素

```shell
${arr[@]}
${arr[*]}

[root@rockylinux-docker ~]# echo ${arr[@]}
100 200

[root@rockylinux-docker ~]# echo ${arr[*]}
100 200
```

获取数组的长度或个数

```shell
${#arr[@]}
${#arr[*]}

[root@rockylinux-docker ~]# echo ${#arr[@]}
2
[root@rockylinux-docker ~]# echo ${#arr[*]}
2
```

获取数组指定index元素的字符长度

```shell
${#arr[index]}

[root@rockylinux-docker ~]# echo ${#arr[1]}
0
[root@rockylinux-docker ~]# echo ${#arr[2]}
3
[root@rockylinux-docker ~]# echo ${#arr[3]}
3

```

### 数组的删除
#### 语法
删除数组指定元素数据

```shell
unset arr[index]
```

删除整个数组

```shell
unset arr
```
