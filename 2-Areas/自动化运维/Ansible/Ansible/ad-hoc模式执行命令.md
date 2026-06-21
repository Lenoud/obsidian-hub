# ad-hoc模式执行命令

Ansible ad-hoc 模式用于快速执行一次性命令。

本文介绍 Ansible 的 Ad-Hoc 模式，即通过命令行快速执行单个任务的方式，涵盖常用模块（yum、shell、service、copy、file、script、group、user、cron、mount、get_url、systemd、selinux、setup）的使用方法与参数说明。

## 特点与对比

在 **Ansible** 中，**Ad-Hoc 模式** 是一种快速执行单个任务的方式，无需创建或使用完整的 **playbook**。这种模式主要用于简单的管理任务或临时性操作，例如检查系统状态、重启服务或分发文件。

### 特点
1. **简洁性**：
    - Ad-Hoc 命令直接通过命令行运行，不需要写复杂的 YAML 文件。
    - 适合执行一次性或调试性质的操作。
2. **即时性**：
    - 操作立即执行，不需要事先编写或维护 playbook。
    - 适合快速测试或执行简单的任务。
3. **灵活性**：
    - 支持多种模块，如 `ping`、`copy`、`command` 等，提供丰富的功能。

### 与 Playbook 的对比
| **特性** | **Ad-Hoc 模式** | **Playbook 模式** |
| --- | --- | --- |
| **用途** | 临时任务，一次性操作 | 复杂任务、重复性操作 |
| **格式** | 直接命令行输入 | YAML 文件 |
| **复杂度** | 简单 | 较复杂 |
| **可重复性** | 不适合 | 适合 |

## 常用模块

### yum模块
用于远程安装软件

state取值：absent, build-dep, fixed, latest, present

```bash
ansible 主机组名 -m yum -a "name=iftop state=present"
```

### shell模块
用于执行shell命令

```bash
ansible 主机组名 -m shell -a "ls /root/"
```

### service模块
用于控制对应服务的状态

```bash
ansible 主机组名 -m service -a "name=docker state=stopped enabled=yes"
```

### copy模块
可以用于将本地文件或目录copy到目标主机

```bash
ansible 主机组名 -m copy -a "src=/root/hosts dest=/etc/hosts"
```

### file模块
可以用于创建文件或者目录

```bash
ansible 主机组名 -m file -a "path=/root/ans_dir_test_cre state=directory"
#创建目录

ansible 主机组名 -m file -a "path=/root/ans_file_test_cre state=touch mode=555 owner=root group=root"
#创建文件，并且设置 用户和组 ，修改对应权限
```

![[1732970269334-01bda2c4-6372-4e10-a936-8632f75f7470.png]]

### script模块
可以在远程主机上执行本地主机中的脚本文件

```bash
ansible 主机组名 -m script -a "/root/script.sh"
```

### group模块
group模块的作用是远程创建用户组

```bash
ansible 主机组名 -m group -a "name=shi_group gid=888 state=present"
ansible 主机组名 -m group -a "name=shi_group gid=888 state=absent"
```

![[1732971124061-6b8cad99-b3e5-4db8-932a-557f325f0d23.png]]

### user模块
用于远程创建用户

| 参数 | 说明 |
| --- | --- |
| `name` | 用户名 |
| `uid` | 用户 UID |
| `group` | 主用户组 |
| `groups` | 附加组(逗号分隔) |
| `password` | 密码(需为 hash,可用 `openssl passwd -6` 生成) |
| `shell` | 登录 shell,如 `/bin/bash`、`/sbin/nologin` |
| `create_home` | 是否创建家目录:`yes` / `no` |
| `state` | `present`(创建,默认)/ `absent`(删除) |

```bash
# 创建用户,指定附加组、登录 shell,不创建家目录
ansible 主机组名 -m user -a "name=shi uid=888 group=shi_group groups=docker shell=/sbin/nologin create_home=no state=present"

# 删除用户
ansible 主机组名 -m user -a "name=shi state=absent remove=yes"
```

![[1732971622129-698f84c7-8d19-4bcd-8727-23011642dfad.png]]

### cron模块
远程添加定时任务

```bash
ansible 主机组名 -m cron -a "minute=00 hour=01 day=* month=* weekday=* name='cron module test' job='/bin/sh /root/a.sh' state=present"
```

### mount模块
远程挂载存储的模块

| 参数 | 说明 |
| --- | --- |
| `path` | 挂载点(目标路径) |
| `src` | 设备或远程源(如 `192.168.100.10:/data`) |
| `fstype` | 文件系统类型:`nfs` / `ext4` / `xfs` 等 |
| `opts` | 挂载选项,如 `defaults`、`rw,noatime` |
| `state` | `mounted`(挂载并写 fstab)/ `present`(仅写 fstab)/ `unmounted`(卸载)/ `absent`(卸载并删 fstab) |

```bash
# 挂载 NFS 并写入 /etc/fstab
ansible 主机组名 -m mount -a "path=/mnt/data src=192.168.100.10:/data fstype=nfs opts=defaults state=mounted"

# 卸载并清理 fstab 条目
ansible 主机组名 -m mount -a "path=/mnt/data state=absent"
```

![[1733031551594-60f9f41a-dc31-4bd2-a454-c81662a5c6f5.png]]

### get_url模块
远程执行wget命令

```bash
 ansible 主机组名 -m get_url  -a "url=https://www.baidu.com dest=/tmp/a.html mode=0666"
```

### systemd模块
远程管理systemd服务

```bash
ansible 主机组名 -m systemd -a "name=docker state=stopped enabled=yes"
```

### selinux模块
远程管理 selinux 开启或关闭

| 参数 | 说明 |
| --- | --- |
| `policy` | SELinux 策略,如 `targeted` |
| `state` | `enforcing` / `permissive` / `disabled` |

```bash
ansible docker -m selinux -a "policy=targeted state=disabled"
```

### setup模块
获取远程主机信息的模块

```bash
 ansible docker -m setup
 #获取所有信息 json格式

 ansible docker -m setup -a "filter=ansible_default_ipv4"
 #获取IPv4信息等

 ansible docker -m setup -a "filter=ansible_memory_mb"
```

![[1733039456031-a8f98c47-4fc5-457e-972d-2ef935b8961d.png]]
