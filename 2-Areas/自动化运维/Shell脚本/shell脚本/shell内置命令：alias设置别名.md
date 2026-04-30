# Shell 内置命令：alias 设置别名

Shell 内置命令 alias 用于设置命令别名。

本文介绍 Shell 内置命令的概念以及 alias 命令的用法，用于给常用复杂命令创建别名以提高工作效率。

## 内置命令介绍

理解内置命令的含义，能掌握alias内置命令，给命令定义别名。

Shell内置命令，就是由BashShell自身提供的命令，而不是文件系统中的可执行脚本文件。

使用type来确定一个命令是否是内置命令

```bash
type 命令
```

cd就是内置命令，而ifconfig是外部命令，是一个脚本文件

```bash
[root@rockylinux-docker ~]# type cd
cd is a shell builtin

[root@rockylinux-docker ~]# type ifconfig
ifconfig is hashed (/usr/sbin/ifconfig)

[root@rockylinux-docker ~]# type docker
docker is /usr/local/bin/docker
```

通常来说，内置命令会比外部命令执行得更快，执行外部命令时不但会触发磁盘I/O，还需要fork出一个单独的进程来执行，执行完成后再退出。而执行内置命令相当于调用当前Shell进程的一个函数，还是在当前Shell环境进程内，减少了上下文切换。

## alias介绍

alisa用于给命令创建别名。

好处：可以将经常操作比较复杂的命令进行设置别名，通过别名的操作提高工作效率

若该命令且不带任何参数，则显示当前SheI进程中的所有别名列表。

```bash
[root@rockylinux-docker ~]# alias
alias cp='cp -i'
alias e='echo'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias xzegrep='xzegrep --color=auto'
alias xzfgrep='xzfgrep --color=auto'
alias xzgrep='xzgrep --color=auto'
alias zegrep='zegrep --color=auto'
alias zfgrep='zfgrep --color=auto'
alias zgrep='zgrep --color=auto'
```

### alias别名定义语法
```bash
alias 别名="命令"

[root@rockylinux-docker ~]# alias e="echo"

[root@rockylinux-docker ~]# e aab
aab
```

这里使用单引号或双引号都可以

### unalias别名删除语法
删除指定的别名

```bash
unalias 命令
```

```bash
unalias e
```
