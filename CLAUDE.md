# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

An Obsidian vault of technical notes (~1017 md files) organized in the **PARA** system, synced to a GitLab-hosted repo and published via the short-star Astro 6 site. Content spans container orchestration, Linux ops, databases, networking, automation, and Go programming.

## Repository Layout

```
1-Projects/    Active work items (网安工作, skyris, 双因素认证恢复码)
2-Areas/       Ongoing technical domains
  容器与编排/    K8s, Docker, Containerd, Istio, CICD, 容器云
  Linux运维/    Linux server ops (storage, networking, firewalls, virtualization)
  数据库/       MySQL, PostgreSQL, Redis, MongoDB, SQLServer, MariaDB, SQL syntax
  网络技术/     Cisco, soft routers, NAT penetration, routing & switching
  自动化运维/   Ansible, Shell scripting
  开发编程/     Git, API, programming basics
  服务部署/     Nginx, Prometheus, RabbitMQ, Clash, Minecraft, misc services
  Windows/      Windows usage tips, bat scripts, Office
3-Resources/   Reference material (interview prep, regex/YAML/cron, AI, public cloud, competition archives)
4-Archive/     Completed/inactive (school courses, graduation thesis, training, OpenStack, old notes)
Go入门指南/    Standalone Go tutorial (211 files, large)
attachments/   Images and file attachments (do not move)
images/        Additional image assets
```

## Conventions

- **PARA ordering**: `1-Projects` → `2-Areas` → `3-Resources` → `4-Archive`. New content goes into the appropriate layer.
- **Image links**: Use Obsidian Wiki format `![[filename.png]]`, not relative markdown paths. Obsidian resolves filenames automatically across the vault.
- **Frontmatter**: Files have `# Title` H1 headings and a one-line intro paragraph.
- **Code blocks**: Use accurate language tags (`bash`, `yaml`, `python`, `toml`, `sql`, `nginx`, `go`). Never use `plain` or `powershell` for shell commands.
- **File naming**: Chinese names with spaces are standard. Do not rename files to ASCII.
- **Git workflow**: Branch `develop` is the working branch. Remote is `ssh://git@git.skyrisai.com:8781/yuzus/obsidian.git`.

## Sync Pipeline

Obsidian vault (this repo) → short-star Astro 6 site. Directory structure changes here affect site navigation.

## Editing Rules

- Do NOT modify `.obsidian/`, `.git/`, `.gitlab/`, `attachments/`, or `images/`.
- When creating new notes, place them in the correct PARA layer based on their nature:
  - Has a deadline/goal? → `1-Projects/`
  - Ongoing responsibility? → `2-Areas/`
  - Reference/interest topic? → `3-Resources/`
  - Completed/superseded? → `4-Archive/`
- Empty or placeholder files (only H1 + intro, no real content) should be deleted or supplemented with actual technical substance.
- Batch modifications should be committed incrementally per logical group, not as one giant commit.
