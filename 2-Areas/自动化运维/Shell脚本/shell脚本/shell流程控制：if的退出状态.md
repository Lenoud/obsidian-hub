# Shell 流程控制：if 的退出状态

Shell if 语句与命令退出状态码（$?）的关系。

本文介绍 Shell 中 if 条件判断与命令退出状态（exit status）的关系，以及如何利用退出状态值控制流程分支。

每个命令执行后都会返回一个退出状态码（0 表示成功，非 0 表示失败），if 语句正是根据这个退出状态来决定执行哪个分支。

## 退出状态基本用法
```bash
# 判断上一条命令是否执行成功
if command; then
  echo "命令执行成功（退出状态为0）"
else
  echo "命令执行失败（退出状态非0）"
fi
```

## 常用退出状态判断
```bash
# 判断文件是否存在
if test -f /etc/hosts; then
  echo "文件存在"
fi

# 判断目录是否存在
if [ -d /tmp ]; then
  echo "目录存在"
fi
```
