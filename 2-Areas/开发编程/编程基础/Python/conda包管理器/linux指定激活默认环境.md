# 自定义默认conda环境
激活 Conda 后自动激活特定环境，你可以编辑 ~/.bashrc 或 ~/.bash_profile 文件，将以下行添加到文件末尾：

```text
conda activate kaifa
```

这样，每次打开新的终端窗口时，都会自动激活 kaifa 环境。

如果你想要在没有明确激活环境时自动激活一个默认环境，你可以使用 auto_activate_base 选项，该选项会在每次启动 shell 时自动激活基础环境。

要启用 auto_activate_base，你可以运行以下命令：

```text
conda config --set auto_activate_base true
```
