# PyInstaller 打包 Python 为 EXE

使用 PyInstaller 将 Python 脚本打包为 Windows 可执行文件。

## 安装

```bash
pip install pyinstaller==6.2.0
```

## 打包命令

```bash
pyinstaller -F -w -i xml.ico nslookupTool.py
```

## 参数说明

| 参数 | 说明 |
| --- | --- |
| `-F` | 生成单个可执行文件 |
| `-w` | 无控制台窗口模式运行 |
| `-i icon.ico` | 指定程序图标 |

## 常见问题

使用 `-w` 参数打包时报错 `win32ctypes.pywin32.pywintypes.error`，解决方案：将 PyInstaller 版本降级为 6.2.0。

```bash
pip uninstall pyinstaller
pip install pyinstaller==6.2.0
```

参考：https://blog.csdn.net/libaineu2004/article/details/112612421
