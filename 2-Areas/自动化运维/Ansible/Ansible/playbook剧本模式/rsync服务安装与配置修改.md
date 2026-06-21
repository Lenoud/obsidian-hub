# rsync服务安装与配置修改

使用 Ansible Playbook 部署 rsync 服务。

本文通过 Ansible Playbook 示例演示如何远程安装和配置 rsync 服务，包括 yaml 剧本文件和 rsyncd.conf 配置文件的编写。

## 测试命令
```bash
ansible-playbook install_rsync.yaml

[root@ansible rsync_playbook]# cat rsync.password
1

rsync -avzn --password-file=./rsync.password ./test_file.txt rsync_backup@192.168.100.140::data
```

## yaml文件
```yaml
- hosts: docker
  remote_user: root
  gather_facts: on
  tasks:
  - name: install software
    yum: name=rsync state=installed

  - name: configure rsync server
    copy: src=./conf/rsyncd.conf dest=/etc/rsyncd.conf
    notify: Restart Rsync Server

  - name: create virt user
    copy: content='rsync_backup:1' dest=/etc/rsyncd.password mode=600

  - name: create yonghu zu www
    group: name=www gid=666

  - name: create yonghu www
    user: name=www uid=666 group=www create_home=on shell=/sbin/nologin

  - name: create data mulu
    file: path=/data state=directory recurse=yes owner=www group=www mode=755

  - name: start rsync server
    service: name=rsyncd state=started enabled=yes

  handlers:
  - name: Restart Rsync Server
    service: name=rsyncd state=restarted
```

## config文件

```bash
[root@ansible rsync_playbook]# cat conf/rsyncd.conf
```

```ini
# rsyncd.conf
# 配置文件路径通常为 /etc/rsyncd.conf

# 全局设置
uid = www
gid = www
port = 873
fake super = yes
use chroot = no
read only = false
log file = /var/log/rsyncd.log
timeout = 900
ignore errors
list = false
auth users = rsync_backup
secrets file = /etc/rsyncd.password

# 模块配置
[data]
path = /data
```

## 说明

| 参数 | 值 | 说明 |
| --- | --- | --- |
| uid/gid | www | rsync 运行的用户和组 |
| port | 873 | rsync 服务监听端口 |
| auth users | rsync_backup | 认证用户名 |
| secrets file | /etc/rsyncd.password | 密码文件路径 |
| path | /data | 共享数据目录 |
