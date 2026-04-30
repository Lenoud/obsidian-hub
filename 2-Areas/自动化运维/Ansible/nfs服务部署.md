# nfs服务部署

使用 Ansible 自动化部署 NFS 服务。

本文演示如何使用 Ansible Playbook 自动化部署 NFS 服务，包括配置 Yum 源、安装 rpcbind 和 nfs-utils、创建共享目录并启动服务。

```yaml
---
- hosts: node1
  remote_user: root
  tasks:
  - name: rm old repo
    shell: rm -rf /etc/yum.repos.d/*

  - name: copy local.repo
    copy:
      src: /root/ansible_nfs/local.repo
      dest: /etc/yum.repos.d/

  - name: install nfs
    yum:
      name:
        - rpcbind
        - nfs-utils
      state: present
    ignore_errors: yes

  - name: mkdir /data/
    file:
      path: /data/
      state: directory

  - name: init nfs
    shell: echo '/data/ 172.16.0.0/24(rw,all_squash)' > /etc/exports

  - name: start rpcbind
    service:
      name: rpcbind
      state: started

  - name: start nfs
    service:
      name: nfs-server
      state: started
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| hosts | node1 | 目标主机组 |
| 共享目录 | /data/ | NFS 共享的数据目录 |
| 共享网段 | 172.16.0.0/24 | 允许访问的网段 |
| 权限 | rw,all_squash | 读写权限，所有用户映射为匿名用户 |
| 依赖服务 | rpcbind, nfs-utils | NFS 所需的基础服务 |
