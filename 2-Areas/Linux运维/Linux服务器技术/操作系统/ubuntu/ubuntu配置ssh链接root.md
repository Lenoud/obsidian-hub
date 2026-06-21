# ubuntu配置ssh链接root

Ubuntu 开启 SSH 服务并允许 root 登录。

```bash
#停止防火墙
ufw disable
#安装ssh服务
apt-get install -y openssh-server

#修改配置文件
gedit /etc/ssh/sshd_config

 PermitRootLogin yes
#修改这段

#重启ssh服务
systemctl restart sshd
```

[SSH配置详解](https://www.yuque.com/u33971054/atri/og0xe0iusid8bd7g)
