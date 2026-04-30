# Winget 包管理工具

Windows Package Manager（winget）是微软官方的命令行包管理工具，用于安装、升级和管理应用程序。

官方文档：[使用 winget 工具安装和管理应用程序](https://learn.microsoft.com/zh-CN/windows/package-manager/winget/)

## 命令列表

| 命令 | 说明 |
| :--- | :--- |
| info | 显示系统元数据（版本号、体系结构、日志位置等） |
| install | 安装指定的应用程序 |
| show | 显示指定应用程序的详细信息 |
| source | 添加、删除和更新 winget 访问的存储库 |
| search | 搜索应用程序 |
| list | 显示已安装的包 |
| upgrade | 升级给定的包 |
| uninstall | 卸载给定的包 |
| hash | 为安装程序生成 SHA256 哈希 |
| validate | 验证清单文件 |
| export | 导出已安装包的列表 |
| import | 将所有包安装到一个文件中 |

## 批量安装

```cmd
winget install Microsoft.WindowsTerminal Microsoft.PowerToys Microsoft.VisualStudioCode
```
