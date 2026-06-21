# Go GUI 编程

Go 语言本身不是 GUI 编程的首选,但社区有几个成熟的 GUI 框架可用于跨平台桌面应用开发。

## 主流 GUI 框架对比

| 框架 | 实现方式 | 跨平台 | 适合 |
| --- | --- | --- | --- |
| **Fyne** | 自绘(Go 绑定 OpenGL/Vulkan) | Win/macOS/Linux/移动端 | 推荐,API 简洁,Material Design 风格 |
| **Wails** | Web 前端 + Go 后端 | Win/macOS/Linux | 熟悉 Web 开发,想用 React/Vue 写 UI |
| **Gio** | 自绘(即时模式 GUI) | Win/macOS/Linux/iOS/Android | 偏实验,性能好 |
| **walk** | Windows 原生 API | 仅 Windows | Windows 专属,体积小 |
| **gotk4/gotk3** | GTK 绑定 | Linux 为主,Win/macOS 较弱 | Linux 工具,依赖 GTK 运行时 |
| **lorca/webview** | 嵌入 Chrome / 系统 WebView | Win/macOS/Linux | 把 Web 应用包装为桌面应用 |

## Fyne:推荐入门

### 安装

```bash
# Go 1.17+
go install fyne.io/fyne/v2/cmd/fyne@latest

# 确认安装
fyne version
```

### Hello World

```go
package main

import (
    "fyne.io/fyne/v2/app"
    "fyne.io/fyne/v2/container"
    "fyne.io/fyne/v2/widget"
)

func main() {
    a := app.New()
    w := a.NewWindow("Hello")

    // 输入框 + 按钮
    entry := widget.NewEntry()
    entry.SetPlaceHolder("请输入名字")

    label := widget.NewLabel("")
    btn := widget.NewButton("打招呼", func() {
        label.SetText("你好, " + entry.Text + "!")
    })

    w.SetContent(container.NewVBox(
        entry,
        btn,
        label,
    ))

    w.Resize(fyne.NewSize(300, 200))
    w.ShowAndRun()
}
```

```bash
# 运行
go run main.go

# 打包成可执行文件
fyne package -os darwin       # macOS
fyne package -os linux        # Linux
fyne package -os windows      # Windows
```

### 常用组件

| 组件 | 创建函数 |
| --- | --- |
| 按钮 | `widget.NewButton(text, onClick)` |
| 标签 | `widget.NewLabel(text)` |
| 输入框 | `widget.NewEntry()` |
| 多行输入 | `widget.NewMultiLineEntry()` |
| 复选框 | `widget.NewCheck(label, onChange)` |
| 下拉选择 | `widget.NewSelect(options, onChange)` |
| 进度条 | `widget.NewProgressBar()` |
| 表格 | `widget.NewTable(...)` |
| 容器 | `container.NewHBox(...)` / `NewVBox` / `NewBorder` |

## Wails:Web 前端 + Go 后端

```bash
# 安装 CLI(需要 Node.js + Go)
go install github.com/wailsapp/wails/v2/cmd/wails@latest

# 新建项目(选模板)
wails init -n myapp -t vue-ts

# 启动热重载开发
cd myapp
wails dev

# 打包
wails build
```

前端用 Vue/React/Svelte 写 UI,Go 写后端业务,通过自动生成的绑定通信:

```go
// app.go
type App struct { ctx context.Context }

func (a *App) Greet(name string) string {
    return "Hello, " + name + "!"
}
```

```typescript
// 前端自动生成的 ts 方法
import { Greet } from "../wailsjs/go/main/App"
const msg = await Greet("World")
```

## 资源链接

- Fyne 官网:<https://fyne.io/>
- Fyne 文档:<https://docs.fyne.io/>
- Wails 官网:<https://wails.io/>
- Gio 官网:<https://gioui.org/>
- Go 圈中文社区:<https://go-circle.cn/>

## 相关笔记
- [[Golang]] — Go 语言基础
- [[Linux下载Go环境]]
- [[不同环境build可执行文件]] — 跨平台编译
- [[MOC-开发编程]]
