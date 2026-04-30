# crudini 命令快速修改 INI 配置文件

`crudini` 是一个专为管理 `.ini` 配置文件的命令行工具，用于快速读取、修改和删除配置文件中的数据。`.ini` 文件是一种常见的配置文件格式，通过分组（Section）和键值对（Key=Value）存储配置内容。`crudini` 简单易用，尤其适合自动化脚本或命令行环境。

---

### `**crudini**`** 的功能简介：**
1. **读取配置**
提取 `.ini` 文件中指定的节（Section）或键值（Key-Value）。
2. **修改配置**
更新或添加新的节、键值。
3. **删除配置**
删除指定的节或键。
4. **创建配置**
如果文件不存在，可以直接生成新的 `.ini` 文件。

---

### `**crudini**`** 的基本语法：**
```bash
crudini --get <文件名> <节名> [键名]
crudini --set <文件名> <节名> <键名> <值>
crudini --del <文件名> <节名> [键名]
```

---

### **命令参数详解：**
+ `**--get**`
获取指定节或键的值。
    - 若只指定节名，返回整个节的内容。
    - 若指定键名，返回对应的值。
+ `**--set**`
设置指定节的键值对。如果节或键不存在，工具会自动创建。
+ `**--del**`
删除指定的节或键。
+ `**--help**`
查看帮助文档，显示所有可用选项。

以下是 `crudini` 常用参数及其说明的表格形式：

| **参数** | **功能说明** |
| --- | --- |
| `--del` | 删除变量 |
| `--existing` | 指定文件已存在 |
| `--format` | 设置输出格式 |
| `--get` | 显示变量值 |
| `--inplace` | 锁定并写入文件 |
| `--list` | 更新一个列表的值 |
| `--list-sep` | 设置自定义间隔符 |
| `--output` | 将输出内容写入指定文件 |
| `--set` | 增加或修改变量 |
| `--verbose` | 显示执行过程详细信息 |

---

### **使用示例：**
#### 示例 `.ini` 文件：
假设有一个名为 `config.ini` 的文件，内容如下：

```bash
[general]
name = openEuler
version = 24.03

[network]
ip = 192.168.1.1
gateway = 192.168.1.254
```

#### 1. **读取配置**
+ 读取整个 `[general]` 节：

```bash
crudini --get config.ini general
```

**输出：**

```bash
name=openEuler
version=24.03
```

+ 读取 `[network]` 节中 `ip` 的值：

```bash
crudini --get config.ini network ip
```

**输出：**

```bash
192.168.1.1
```

#### 2. **设置配置**
+ 修改 `[general]` 节中 `name` 的值为 `EulerOS`：

```bash
crudini --set config.ini general name EulerOS
```

文件更新为：

```bash
[general]
name = EulerOS
version = 24.03

[network]
ip = 192.168.1.1
gateway = 192.168.1.254
```

+ 添加新节 `[database]` 并设置键值：

```bash
crudini --set config.ini database user admin
```

文件更新为：

```bash
[database]
user = admin
```

#### 3. **删除配置**
+ 删除 `[network]` 节：

```bash
crudini --del config.ini network
```

文件更新为：

```bash
[general]
name = EulerOS
version = 24.03
```

+ 删除 `[general]` 节中的 `version` 键：

```bash
crudini --del config.ini general version
```

文件更新为：

```bash
[general]
name = EulerOS
```

#### 4. **自动创建文件**
如果目标文件不存在，`crudini` 会自动创建文件。例如：

```bash
crudini --set new_config.ini settings key value
```

生成的文件内容：

```bash
[settings]
key = value
```

---

### **常见应用场景：**
1. 自动化脚本中动态修改配置文件。
2. 无需手动编辑 `.ini` 文件即可完成复杂配置更新。
3. 快速检索配置内容。

---

### `**crudini**`** 安装：**
大多数 Linux 发行版的默认仓库中已包含 `crudini`，可以直接安装：

+ **Debian/Ubuntu 系统**：

```bash
sudo apt install crudini
```

+ **RHEL/CentOS/Fedora 系统**：

```bash
sudo dnf install crudini
```

+ **openEuler 系统**：

```bash
sudo yum install crudini
```

#### 可能出现的错误
如果使用dnf（yum）或者apt安装crudini 报错（例如： SyntaxError: Missing parentheses in call to 'print'. Did you mean print(...)?  ）有可能是没有python2环境，或者指定的python2环境错误

使用 `vi /usr/bin/crudini`命令修改第一行 `#!/usr/bin/python2` 指定python2解释器路径位置

```bash
vi /usr/bin/crudini

#!/usr/bin/python2
```

或者使用

```bash
python3 -m pip install crudini
```

命令安装python3版本的crudini

---
