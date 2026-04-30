# Windows 定时关机任务

通过任务计划程序或命令行设置 Windows 定时关机。

## 任务计划程序方式

1. `Win+R` 输入 `taskschd.msc`
2. 创建基本任务 → 命名"定时关机" → 每天 → 设置关机时间
3. 勾选"启动程序" → 输入 `C:\Windows\System32\shutdown.exe -s`

如需关闭或修改：`taskschd.msc` → 任务计划程序库 → 修改或删除定时关机任务。

## 命令行方式

```cmd
Shutdown -s -t 20000
```

20000 秒后自动关机。
