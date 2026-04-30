# 刚装好的kali想要开放root远程登录权限需要修改sshd配置

kali相关技术笔记。
```bash
┌──(root㉿kali-server)-[~]
└─# cat /etc/ssh/sshd_config |grep Root
PermitRootLogin yes
```
