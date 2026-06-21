# shell内置命令：read读取控制台输入

Shell 内置命令 read 用于读取控制台输入。

### 目标
理解read命令的作用

使用read给多个变量赋值

使用read读取1个字符

使用read限制时间输入

### 介绍
read是Shell内置命令，用于从标准输入中读取数据并赋值给变量。如果没有进行重定向，默认就是从终端控制台读取用户输入的数据；如果进行了重定向，那么可以从文件中读取数据。

### 语法
```text
read [-options] [var1 var2 ....]
```

options表示选项，如下表所示；var表示用来存储数据的变量，可以有一个，也可以有多个。

options和var都是可选的，如果没有提供变量名，那么读取的数据将存放到环境变量REPLY变量中。$REPLY保存read最后一个读入命令的数据

#### options支持的参数
| **选项** | **描述** |
| --- | --- |
| `-a array` | 将读取的内容按空格分隔并存储到数组中。 |
| `-d delim` | 使用指定的分隔符读取输入，而不是默认的换行符。 |
| `-e` | 启用行编辑功能，允许使用箭头键编辑输入（需要 `readline`支持）。 |
| `-n num` | 读取指定数量的字符，而不是整行。 |
| `-p prompt` | 在读取输入之前显示提示文本,提示类容为 prompt |
| `-r` | 原样读取输入，不处理反斜杠转义字符。 |
| `-s` | 静默模式，不会将输入内容回显到屏幕上（适合读取密码）。 |
| `-t timeout` | 指定超时时间（秒）。如果在超时时间内没有输入内容，`read`将返回非零状态。 |
| `-u fd` | 从指定的文件描述符读取，而不是标准输入。 |

#### 示例1：多个变量赋值
##### 需求
使用read给多个变量赋值

##### 演示
```bash
#!/bin/bash
read -p "input you username and password: " username mima

echo "输入的用户名:${username}"
echo "输入的密码:${mima}"

[root@rockylinux-docker shell]# bash read_shell.sh
input you username and password: liubiao 123456
输入的用户名:liubiao
输入的密码:123456

```

#### 示例2：读取一个字符
##### 需求
从控制台只读取一个字符

##### 演示
```bash
[root@rockylinux-docker shell]# cat read_shell2.sh
#!/bin/bash
read -p "你要删除数据吗？（y/n）:" -n 1 char

printf "\n"

echo "输入的值为：${char}"

[root@rockylinux-docker shell]# bash read_shell2.sh
你要删除数据吗？（y/n）:y
输入的值为：y

```

#### 示例3：限制时间输入
##### 需求
在终端控制台输入时，设置指定时间内输入密码

##### 演示
```bash
#!/bin/bash
read -s -t 10 -p "请输入密码：" passwd1
echo ""  # 换行

read -s -t 10 -p "请再次输入密码：" passwd2
echo ""  # 换行

if [[ "${passwd1}" == "${passwd2}" ]]; then
    echo "输入的密码一致：${passwd1}"
else
    echo "输入的密码不一样"
fi

```
