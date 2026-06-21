# Windows BAT 脚本

Windows 批处理脚本(Batch / .bat / .cmd)是 Windows 内置的脚本语言,通过 cmd.exe 解释执行。适合系统管理、自动化任务、简单工具封装,复杂场景建议用 PowerShell。

## 基本语法

### Hello World

```bat
@echo off
echo Hello, World!
pause
```

| 命令 | 作用 |
| --- | --- |
| `@echo off` | 关闭命令回显(脚本执行时不显示每条命令本身) |
| `echo text` | 输出文本 |
| `pause` | 暂停,等用户按任意键 |
| `rem ...` 或 `:: ...` | 注释(推荐 `::`) |

## 变量

```bat
@echo off
:: 赋值(等号两边不能有空格)
set NAME=world

:: 引用变量要用 %% 包裹
echo Hello, %NAME%

:: 算术运算(/a 表示 arithmetic)
set /a x=5+3
echo 5+3=%x%

:: 用户输入(/p 表示 prompt)
set /p age=请输入年龄:
echo 你的年龄是 %age%
```

## 流程控制

### if / else

```bat
@echo off
set NUM=10

if %NUM% gtr 5 (
    echo NUM 大于 5
) else (
    echo NUM 小于等于 5
)

:: 字符串比较
if "%1"=="" (
    echo 请传入参数
    exit /b 1
)

:: 文件存在判断
if exist C:\Windows\System32\cmd.exe (
    echo 找到 cmd.exe
)
```

if 比较运算符:

| 运算符 | 含义 |
| --- | --- |
| `equ` / `==` | 等于 |
| `neq` | 不等于 |
| `gtr` / `geq` | 大于 / 大于等于 |
| `lss` / `leq` | 小于 / 小于等于 |
| `exist <file>` | 文件存在 |
| `defined <var>` | 变量已定义 |

### for 循环

```bat
@echo off
:: 遍历数字
for /l %%i in (1,1,5) do (
    echo %%i
)

:: 遍历目录中的 .txt 文件
for %%f in (*.txt) do (
    echo 处理 %%f
)

:: 遍历目录(/r 递归)
for /r C:\logs %%f in (*.log) do (
    del "%%f"
)
```

## 常用命令

| 命令 | 作用 |
| --- | --- |
| `mkdir <dir>` / `md` | 创建目录 |
| `rmdir /s /q <dir>` / `rd` | 递归删除目录(/q 不确认) |
| `copy a b` | 复制文件 |
| `xcopy /e /i /y src dst` | 递归复制目录 |
| `robocopy src dst /mir` | 更强的目录同步(支持断点续传) |
| `del <file>` | 删除文件 |
| `move a b` | 移动/重命名 |
| `type <file>` | 显示文件内容(类似 cat) |
| `findstr "pattern" <file>` | 搜索文本(类似 grep) |
| `timeout /t 5` | 等待 5 秒 |
| `start "" notepad.exe` | 后台启动程序 |
| `tasklist` | 列出进程 |
| `taskkill /im notepad.exe /f` | 强制结束进程 |

## 实用示例

### 1. 快速打开网卡设备管理面板

```bat
@echo off
ncpa.cpl
```

### 2. 每天定时备份目录

```bat
@echo off
set SRC=C:\data
set DST=D:\backup\%date:~0,4%%date:~5,2%%date:~8,2%
robocopy "%SRC%" "%DST%" /mir /log+:D:\backup\log.txt
echo 备份完成:%DST%
```

(配合 Windows 任务计划程序定时执行)

### 3. 检测服务是否运行,否则重启

```bat
@echo off
sc query W3SVC | find "RUNNING" >nul
if errorlevel 1 (
    echo W3SVC 未运行,正在启动...
    net start W3SVC
)
```

## 与 PowerShell 的对比

| 维度 | BAT | PowerShell |
| --- | --- | --- |
| 解释器 | cmd.exe | powershell.exe / pwsh.exe |
| 对象模型 | 字符串 | 强类型对象(.NET) |
| 管道 | 纯文本管道 | 对象管道 |
| 跨平台 | 仅 Windows | Windows / Linux / macOS(PowerShell 7) |
| 现代化 | 停滞 | 持续演进 |
| 适合 | 简单脚本、兼容性 | 复杂运维、现代 Windows |

> 新项目**建议直接用 PowerShell**;BAT 仅在需要兼容老系统或运行环境受限时使用。

## 相关笔记
- [[快速打开网卡设备管理面板]] — 单行 bat 脚本示例
- [[MOC-Windows]]
