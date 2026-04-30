# Debian

Debian Linux 操作系统基础配置与使用。

## 目录和文件没有颜色
```bash
export LS_OPTIONS='--color=auto'
eval "`dircolors`"
alias ls='ls $LS_OPTIONS'
alias ll='ls $LS_OPTIONS -l'
```
