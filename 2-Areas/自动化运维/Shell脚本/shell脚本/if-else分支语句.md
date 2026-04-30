# if-else 分支语句

Shell 脚本 if-else 分支语句语法。

本文介绍 Shell 脚本中 if-else 分支语句的使用方法，包括检测文件是否存在、单分支判断等常见场景的代码示例。

## 判断密钥文件是否存在

```bash
#生成密钥文件
keyFile=~/.ssh/id_rsa
if test -f "$keyFile"; then
  echo -e "\e[31mssh密钥文件存在\e[0m"
else
  echo -e "\e[31m生成ssh密钥文件\e[0m"
  ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
fi
```

## 单分支判断

```bash
#单分支
control=yes
if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"
```
