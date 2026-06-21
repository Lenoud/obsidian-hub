# pipreqs 导出项目依赖

`pipreqs` 是一个 Python 工具,扫描项目源码中的 `import` 语句,生成只包含**项目实际使用**的依赖清单 `requirements.txt`。

## 与 `pip freeze` 的区别

| 工具 | 原理 | 适用场景 |
| --- | --- | --- |
| `pip freeze > requirements.txt` | 导出当前虚拟环境所有包 | 部署到相同环境 |
| `pipreqs /path/to/project` | 扫描源码 `import` | 项目迁移、最小化依赖 |

`pip freeze` 会导出几十上百个间接依赖;`pipreqs` 只导出顶层依赖。

## 安装

```bash
pip install pipreqs
```

## 基本用法

```bash
# 在项目根目录执行(会生成 ./requirements.txt)
pipreqs /path/to/project

# 指定输出路径
pipreqs /path/to/project --savepath /tmp/requirements.txt

# 强制覆盖已存在的 requirements.txt
pipreqs /path/to/project --force

# 只输出到终端,不写文件
pipreqs /path/to/project --print
```

## 常用选项

| 选项 | 说明 |
| --- | --- |
| `--force` | 覆盖已存在的 requirements.txt |
| `--print` | 只打印,不写文件 |
| `--savepath <file>` | 自定义输出路径 |
| `--encoding <charset>` | 指定源码字符集(默认 utf-8) |
| `--pypi-server <url>` | 自定义 PyPI 服务(内网镜像) |
| `--ignore <dirs>` | 忽略目录(逗号分隔) |

## 典型工作流

```bash
# 1. 在项目目录生成依赖
cd /path/to/myproject
pipreqs . --force
# 输出示例:
# INFO: Found packages: flask, requests, pyyaml
# INFO: Successfully saved requirements to ./requirements.txt

# 2. 在新环境重建
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## 常见问题

| 问题 | 原因 | 解决 |
| --- | --- | --- |
| `UnicodeDecodeError` | 源码字符集非 utf-8 | `pipreqs . --encoding=gbk` |
| 包名不正确(如 `Pillow` 写成 `pillow`) | 包名大小写敏感 | 手动修 requirements.txt |
| 漏掉包 | 动态 import(`__import__`、`importlib`) | 补充手动检查 |
| 把开发依赖也扫进去 | 扫描了 tests/ 目录 | `pipreqs . --ignore=tests,docs` |

## 相关笔记
- [[pip]] — pip 命令全集
- [[常用的命令]] — pip 常用命令
- [[conda包管理器]] — 替代方案
- [[MOC-开发编程]]
