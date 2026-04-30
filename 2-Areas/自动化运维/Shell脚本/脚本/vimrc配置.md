# Vim 编辑器 vimrc 配置

本文介绍 Vim 编辑器的 vimrc 配置方法，包括自动添加 shell 脚本头的配置。

## 自动添加脚本头

在 vimrc 中配置创建 .sh 文件时自动添加 `#!/bin/bash` 头部：

```bash
#vim创建sh文件时自动添加#!/bin/bash
autocmd BufNewFile *.sh 0put =\"#!/bin/bash\<nl>\" | $
```
