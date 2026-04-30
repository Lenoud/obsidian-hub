# SSH 免密登录

SSH 免密登录的配置方法。

本文介绍如何快速生成 SSH-RSA 密钥文件，用于实现主机间的免密登录。

## 快速生成ssh-rsa秘钥文件
```bash
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```
