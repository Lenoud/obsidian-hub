# Ansible

Ansible 自动化运维工具的安装与基本使用。

本文介绍 Ansible 的安装配置方法，以及常用模块（service、yum、shell、copy）在 playbook 中的使用示例。

## 安装与配置

```bash
#安装ansible
yum -y install ansible

#配置文件存放位置
cat /etc/ansible/hosts
[elk-1]
192.168.100.217

#检查连通性
ansible 192.168.100.217,elk-2,elk-3 -m ping

#生成角色目录
ansible-galaxy init init

ansible HBase02,192.168.50.71 -m shell -a "ls /root/"
```

## Ansible-service模块
一、service模块

service模块：用于控制服务的启动,关闭,开机自启动等。

[https://docs.ansible.com/ansible/latest/modules/service_module.html#service-module](https://docs.ansible.com/ansible/latest/modules/service_module.html#service-module)

参数

name

服务名称

state  reloaded, restarted, started, stopped

服务管理

enabled yes|no

开启是否启动

## yum安装
```yaml
[root@ansible zbbix]# cat mariadb.yaml
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
      state: started  #启动服务
      enabled: yes  #开机自启
```

## 执行shell命令
```yaml
cat shell.yml

- hosts: all
  tasks:
    - name: Run zkServer.sh script
      shell: zkServer.sh start
      args:
        chdir: /usr/local/zookeeper/bin

    - name: Status zkServer.sh script
      shell: zkServer.sh status
      args:
        chdir: /usr/local/zookeeper/bin
```

## 批量copy
```yaml
---
- name: Copy file to remote hosts
  hosts: HBase_node  # 目标主机的组名或主机名
  tasks:
    - name: Copy config remote hosts
      copy:
        src: /opt/zookeeper-3.7.0/conf/zoo.cfg  # 当前目录中的文件
        dest: /opt/zookeeper-3.7.0/conf  # 目标主机上的路径
```
