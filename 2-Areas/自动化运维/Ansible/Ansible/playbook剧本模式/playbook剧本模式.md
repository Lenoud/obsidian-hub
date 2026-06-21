# Ansible Playbook 剧本模式

Playbook 是 Ansible 的核心,使用 YAML 描述一组有序的任务(tasks),并通过 SSH 推送到远程主机执行。相比 ad-hoc 模式,Playbook 适合**可复用、可版本化、复杂多任务**的自动化场景。

## 基本结构

```yaml
---
- name: 部署 Nginx Web 服务        # 顶层 name(play 名)
  hosts: web                       # 目标主机组(来自 inventory)
  remote_user: root                # 远程登录用户
  become: yes                      # 是否 sudo 提权
  gather_facts: yes                # 是否收集主机信息(默认 yes)

  vars:                            # 变量定义
    http_port: 80
    doc_root: /var/www/html

  tasks:                           # 任务列表(顺序执行)
    - name: 安装 nginx             # 每个 task 必须有 name
      yum:
        name: nginx
        state: present

    - name: 启动并设置开机自启
      service:
        name: nginx
        state: started
        enabled: yes

    - name: 部署 index.html
      copy:
        src: files/index.html
        dest: "{{ doc_root }}/index.html"
      notify: Reload Nginx         # 通知 handler

  handlers:                        # 在所有 tasks 之后执行,且仅在 notified 时触发
    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

## 核心组件

| 组件 | 作用 |
| --- | --- |
| `hosts` | 目标主机组,来自 inventory 文件 |
| `vars` | 变量,可在模板、task 中用 `{{ var }}` 引用 |
| `tasks` | 任务列表,按顺序执行 |
| `handlers` | 类似 task,但只在被 `notify` 时触发,通常用于重启 |
| `templates` | Jinja2 模板,配合 `template` 模块下发动态配置 |
| `roles` | 角色化组织,把 tasks/vars/files/templates 按目录拆分 |

## 运行命令

```bash
# 执行 playbook
ansible-playbook deploy.yaml

# 常用选项
ansible-playbook -i inventory deploy.yaml         # 指定 inventory
ansible-playbook -u root -k deploy.yaml           # 指定用户、交互式输密码
ansible-playbook --tags "install" deploy.yaml     # 只跑带 tag 的 task
ansible-playbook --check deploy.yaml             # 试运行,不实际修改
ansible-playbook --step deploy.yaml              # 单步,每步问是否继续
ansible-playbook -v deploy.yaml                  # 详细输出(-vv / -vvv 更详细)
```

## 执行流程

```text
1. 读 inventory,确定目标主机
2. 对每台主机并行执行 tasks:
   a. SSH 连接(或 cached connection)
   b. 收集 facts(gather_facts: yes 时)
   c. 依次执行 task:
      - 模块执行 → 返回 JSON 结果(包含 changed: true/false)
      - 如果 changed 且有 notify → 把 handler 名暂存
3. 所有 task 完成后,触发所有暂存的 handler(每个只触发一次)
4. 生成执行总结:成功/失败/changed/ok 数量
```

## 变量与模板

```yaml
# vars 区定义
vars:
  domain: example.com
  max_clients: 200

# 引用变量
tasks:
  - name: 部署 nginx.conf
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf

# templates/nginx.conf.j2(Jinja2 模板)
# worker_processes auto;
# server {
#     server_name {{ domain }};
#     worker_connections {{ max_clients }};
# }
```

## 与 Ad-Hoc 的对比

| 维度 | Ad-Hoc(`ansible -m`) | Playbook |
| --- | --- | --- |
| 形式 | 命令行单条命令 | YAML 文件 |
| 复用性 | 一次性 | 可版本化、可分享 |
| 复杂度 | 单模块调用 | 多任务、变量、模板、handler |
| 控制流 | 无 | 条件(`when`)、循环(`loop`)、错误处理(`block/rescue`) |

## 相关笔记
- [[ad-hoc模式执行命令]] — 单条命令模式
- [[Ansible]] — Ansible 总览
- [[rsync服务安装与配置修改]] — 实战示例
- [[自动化工具介绍]]
- [[MOC-自动化运维]]
