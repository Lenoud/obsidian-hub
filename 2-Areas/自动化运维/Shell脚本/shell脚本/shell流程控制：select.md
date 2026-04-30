# Shell 流程控制：select

Shell 流程控制 select 菜单选择语句。

本文介绍 Shell 中 select in 循环语句的用法，用于显示带编号的交互式菜单，用户输入编号即可选择对应功能，配合 case 语句实现菜单选择。

能够使用select语句进行菜单选择输入

## 说明
> select in 循环用来增强交互性，它可以显示出带编号的菜单，用户输入不同的编号就可以选择不同的菜单，并执行不同的功能。select in 是 Shell 独有的一种循环，非常适合终端（Terminal）这样的交互场景，其他语言没有

## 语法
```bash
select var in menu1 menu2 ...
do
 command1
done
```

> 注意：select是无限循环（死循环），输入空值，或者输入的值无效，都不会结束循环，只有遇到break语句，或者按下Ctrl+D组合键才能结束循环。
>
> 执行命令过程中：终端会输出#？代表可以输入选择的菜单编号

## 示例
```bash
#!/bin/bash
echo "你喜欢的语言是哪一门？"

select le in  "java" "python" "golang" "shell" "web" "javascript" "退出"
do
  case $le in
    "javascript")
      echo "课程：${le}"
      break
      ;;
    "java")
      echo "课程：${le}, 面向对象语言"
      break
      ;;
    "python")
      echo "课程：${le}, 面向对象语言"
      break
      ;;
    "golang")
      echo "课程：${le}"
      break
      ;;
    "shell")
      echo "课程：${le}"
      break
      ;;
    "web")
      echo "课程：${le}"
      break
      ;;
    "退出")
      echo "退出程序"
      break
      ;;
    *)
      echo "输入有误，请重新选择"
      ;;
  esac
done

[root@ansible shell]# bash select2.sh
你喜欢的语言是哪一门？
1) java
2) python
3) golang
4) shell
5) web
6) javascript
7) 退出
#? 1
课程：java, 面向对象语言
```
