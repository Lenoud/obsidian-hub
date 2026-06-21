# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

An Obsidian vault of technical notes organized in the **PARA** system + **MOC**(Map of Content)索引体系,hosted on GitHub and published via the short-star Astro 6 site. Content spans container orchestration, Linux ops, databases, networking, automation, and Go programming.

## Repository Layout

```
1-Projects/    Active work items (网安工作, skyris, 双因素认证恢复码)
2-Areas/       Ongoing technical domains
  CICD/          Drone, GitLab-CI, Jenkins
  Docker/        Docker, Docker-Compose, Containerd, Registry, 项目实战
  Kubernetes/    K8s 基础/Istio/KubeVirt/dry-run/开发
  Linux运维/     存储(Ceph/GlusterFS/NFS)、操作系统、虚拟化、防火墙
  Windows/       Windows 使用技巧、bat、Office
  开发编程/      Git、API、Golang、Java、Python、前端
  数据库/        MySQL、PostgreSQL、Redis、MongoDB、MariaDB、SQLServer、SQL 语法
  服务部署/      Nginx、Prometheus、RabbitMQ、Clash、MC、邮件、宝塔 等
  网络技术/      Cisco、软路由、内网穿透、路由交换
  自动化运维/    Ansible、Shell 脚本(40+ 篇)
3-Resources/   Reference material (面试题、赛事参考、cron/YAML/Markdown 表达式、公有云)
4-Archive/     Completed/inactive (school courses, graduation thesis, training, OpenStack, old notes)
Go入门指南/    Standalone Go tutorial (211 files, large)
attachments/   Images and file attachments (do not move)
images/        Additional image assets
docs/          文档质量排查日志(本仓库的文档维护工作记录)
```

## Methodology — PARA + MOC + 双向链接

仓库采用三层方法论,**三层都必须遵守**:

1. **PARA 分类**(目录层):新笔记按性质进 `1-Projects/` / `2-Areas/` / `3-Resources/` / `4-Archive/`。
2. **MOC 索引**(导航层):每个一级领域有 `MOC-<领域>.md`(如 [MOC-Docker.md](2-Areas/Docker/MOC-Docker.md)),根目录有 [MOC-Home.md](MOC-Home.md) 作为总入口。新笔记必须在对应 MOC 中登记。
3. **Wiki 双向链接**(网状层):每篇笔记尾部必须有 `## 相关笔记` 段,通过 `[[xxx]]` 链接到 2-4 篇相关笔记,避免成为孤岛。

## Conventions

- **PARA ordering**: `1-Projects` → `2-Areas` → `3-Resources` → `4-Archive`. New content goes into the appropriate layer.
- **Image links**: Use Obsidian Wiki format `![[filename.png]]`, not relative markdown paths. Obsidian resolves filenames automatically across the vault.
- **Frontmatter**: Files have `# Title` H1 headings and a one-line intro paragraph.
- **Code blocks**: Use accurate language tags (`bash`, `yaml`, `python`, `toml`, `sql`, `nginx`, `go`, `ini`, `text`). **Never** use `plain` / `shell` / `sh` / `txt`. For PowerShell scripts use `powershell`; for `.bat` use `bat`.
- **Cisco IOS / 网络设备命令**: 使用 `text` 标签(没有通用的 cisco 标签),**不要**误用 `bash`。
- **File naming**: Chinese names with spaces are standard. Do not rename files to ASCII.
- **Git workflow**: 主分支为 `main`。Remote 是 `git@github.com:Lenoud/obsidian-hub.git`。
- **每个逻辑组一个 commit**:批量修改按主题分组提交(如"修 Cisco 断链"、"kali 重写"),**禁止**一个大 commit 混多个不相关主题。

## 笔记内容结构模板(技术参考型)

新笔记或重写笔记时,按此模板组织:

```markdown
# <标题>

<一行导语:这个主题是什么、解决什么问题。不要用"相关技术笔记"这种空泛话>

## <分类章节 1>
<命令/配置,多用表格、代码块>

## <分类章节 2>
...

## 常见问题 / 注意事项
<可选,表格或列表>

## 相关笔记
- [[xxx]] — 简要说明关联点
- [[yyy]]
- [[MOC-<领域>]]
```

**禁止的导语**:"xxx 相关技术笔记"、"xxx 学习笔记"(没有信息量)。
**推荐的导语**:具体说明该主题的用途、覆盖范围、关键特性。

## Sync Pipeline

Obsidian vault (this repo) → short-star Astro 6 site. Directory structure changes here affect site navigation.

## Editing Rules

- Do NOT modify `.obsidian/`, `.git/`, `.gitlab/`, `attachments/`, or `images/`.
- When creating new notes, place them in the correct PARA layer based on their nature:
  - Has a deadline/goal? → `1-Projects/`
  - Ongoing responsibility? → `2-Areas/`
  - Reference/interest topic? → `3-Resources/`
  - Completed/superseded? → `4-Archive/`
- **Empty or placeholder files**(only H1 + intro, no real content) **必须处理**:删除,或补充为真实技术内容。目录同名占位文件(职能应归 MOC)一律删除。
- **代码块标签错误**(`plain`/`shell`/`sh`/`txt` / Cisco IOS 误用 `bash` / YAML 内容误用 `bash` 等)发现即修。
- **笔记间断链**(`[[xxx]]` 指向不存在的文件)发现即修:改名、删除链接、或补建目标文件。
- **MOC 中的断链**优先修复:MOC 是入口,断链直接影响导航。
- Batch modifications should be committed incrementally per logical group, not as one giant commit.

## 维护文档

- [docs/文档质量排查日志.md](docs/文档质量排查日志.md):全仓库文档质量排查与修复的**唯一事实来源**,记录每一轮的 commit、影响范围、验证结果。新的一轮排查开始前先读此文档。
