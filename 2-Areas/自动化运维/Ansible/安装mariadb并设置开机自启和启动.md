# 安装mariadb并设置开机自启和启动

使用 Ansible 自动化安装 MariaDB 并设置开机自启。

本文演示如何使用 Ansible Playbook 在远程主机上安装 MariaDB 数据库，并配置服务启动和开机自启。

```yaml
- name: server node install mariadb-server
  hosts: server
  remote_user: root
  tasks:
  - name: install db
    yum:
      name: mariadb-server
      state: present
  - name: start and enabled  db-server
    service:
      name: mariadb
      state: started #启动
      enabled: yes #开机自启
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| hosts | server | 目标主机组 |
| yum name | mariadb-server | 安装的软件包 |
| service state | started | 启动 MariaDB 服务 |
| service enabled | yes | 设置开机自启 |
