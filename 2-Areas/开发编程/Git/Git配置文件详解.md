# Git 配置文件详解

Git 配置文件分三个级别，优先级从高到低为：仓库级 > 用户级 > 系统级。

## Git 配置文件级别
Git 的配置文件主要有三个级别，即如下表：

| **配置级别** | **配置文件** | **英文描述** | **优先级** |
| --- | --- | --- | --- |
| 仓库级别 | /etc/gitconfig | local | 优先级最高 |
| 用户级别 | ~/.gitconfig | global | 优先级次之 |
| 系统级别 | .git/config | system | 优先级最低 |

## Git config命令详解
### 增加配置
#### 语法
```plain
git config [--local|--global|--system] --add section.key value
```

#### 说明
增加配置项的参数为 --add，注意 add 后面的 section, key, value 一项都不能少，否则添加失败。

### 获取配置
#### 语法
```plain
git config [--local|--global|--system] --get section.key
```

#### 说明
获取配置项的参数为 --get，如果获取一个 section 不存在的 key 值，不会返回任何值，如果获取一个不存在的 section 的 key 值，则会报错。

### 删除配置
#### 语法
```plain
git config [--local|--global|--system] --unset section.key
```

#### 说明
删除配置项的参数为 --unset。

## Git config查看配置详解
### 说明
我们使用 git config -l 命令，可以查看 git 的配置信息。

git config --list --show-origin查看配置文件位置和详情

### 语法
| **命令** | **描述** |
| --- | --- |
| git config --local -l | 查看仓库配置 |
| git config --global -l | 查看用户配置 |
| git config --system -l | 查看系统配置 |
| git config -l | 查看所有的配置信息，依次是系统级别、用户级别、仓库级别 |

## Git config编辑配置详解
### 说明
我们使用 git config -e 命令，可以编辑 git 的配置信息。

### 语法
| **命令** | **描述** |
| --- | --- |
| git config --local -e | 编辑仓库级别配置文件 |
| git config --global -e | 编辑用户级别配置文件 |
| git config --system -e | 编辑系统级别配置文件 |

## Git config添加配置详解
### 说明
我们使用 git config 命令，可以添加 git 的配置信息。

### 语法
| **命令** | **描述** |
| --- | --- |
| git config --global user.email “eamil” | 添加 email 配置 |
| git config --global user.name “username” | 添加用户名配置 |

## 配置文件生效
对于 git 来说，配置文件的权重是 仓库 > 全局 > 系统。Git 会使用这一系列的配置文件来存储你定义的偏好，它首先会查找 /etc/gitconfig 文件（系统级），该文件含有对系统上所有用户及他们所拥有的仓库都生效的配置值。

接下来 Git 会查找每个用户的 ~/.gitconfig 文件（全局级）。最后 Git 会查找由用户定义的各个库中 Git 目录下的配置文件 .git/config（仓库级），该文件中的值只对当前所属仓库有效。

## 增加配置
### 语法
```plain
git config [--local|--global|--system] --add section.key value
```

### 案例
我们首先创建一个空文件夹，接着，我们打开 git 命令行工具并使用 git init 命令，初始化一个空仓库，具体命令如下：

```plain
git init
```

执行完成后，终端如下图所示：

![[1702973159782-5ef94522-01a8-44f5-84bd-cbd5768bad05.png]]

现在，我们使用 git config 命令，添加一个配置项，具体命令如下：

```plain
git config --local --add module.url haicodernet
```

执行完成后，终端如下图所示：

![[1702973159839-c6f55583-2c0b-432b-aa3c-4612e0433763.png]]

即，我们成功添加了一个配置项，现在，我们可以使用 git config -l 命令，查看配置项，具体命令如下：

```plain
git config --local -l
```

执行完成后，终端如下图所示：

![[1702973159772-88649420-1cd2-4b88-9a2b-e171bff517d0.png]]

我们看到，此时配置项里面就有我们刚增加的配置项了。

## 获取配置
### 语法
```plain
git config [--local|--global|--system] --get section.key
```

### 案例
我们使用 git config --get 命令，获取刚刚上面设置的配置，具体命令如下：

```plain
git config --local --get module.url
```

执行完成后，终端如下图所示：

![[1702973159773-e363a8ad-9c99-4293-8de9-351597fbf618.png]]

我们看到，我们使用了 git config --get 获取了我们刚刚添加的配置项。

## 删除配置
### 语法
```plain
git config [--local|--global|--system] --unset section.key
```

### 案例
我们使用 git config --unset 命令，删除刚刚上面设置的配置，具体命令如下：

```plain
git config --local --unset module.url
```

执行完成后，终端如下图所示：

![[1702973159775-426c76a6-557d-4a33-b3b4-a274c3a844ce.png]]

现在，我们再次使用 git config --get 获取该配置项，具体命令如下：

```plain
git config --local --get module.url
```

执行完成后，终端如下图所示：

![[1702973160185-b2ae8854-6943-45b8-a12c-c43dd3ca1efb.png]]

我们看到，此时刚刚我们删除的配置项已经不存在了。

## 查看配置
### 语法
```plain
git config [--local|--global|--system] -l
```

### 案例
我们使用 git config -l 命令，查看配置，具体命令如下：

```plain
git config -l
```

执行完成后，终端如下图所示：

![[1702973160205-d8600bbf-80fa-44a0-bd1b-7b68676b98aa.png]]

我们看到，此时输出了全部的配置项，我们还可以指定我们需要查看的配置项，具体命令如下：

```plain
git config --local -l
```

执行完成后，终端如下图所示：

![[1702973160231-a8100757-e2a2-40fc-91b3-c84b5b8892dc.png]]

我们看到，此时只输出了我们本地的配置了。

## 编辑配置
### 语法
```plain
git config [--local|--global|--system] -e
```

### 案例
我们使用 git config -e 命令，编辑配置，具体命令如下：

```plain
git config -e
```

执行完成后，终端如下图所示：

![[1702973160243-e9c33fed-41e2-4dee-861c-00eacfdc6a9b.png]]

我们看到，此时使用 [vim](https://haicoder.net/vim/vim-tutorial.html) 打开了配置，我们可以直接修改，保存并退出。

## 添加配置
### 语法
```plain
git config [--local|--global|--system] section.key value
```

### 案例
我们使用 git config 命令，添加配置，具体命令如下：

```plain
git config --local haicoder.url www.haicoder.net
```

执行完成后，终端如下图所示：

![[1702973160227-c3a7f3b4-d781-422e-a68b-47b00d3f179a.png]]

现在，我们使用 git config 命令，查看配置，具体命令如下：

```plain
git config --local -l
```

执行完成后，终端如下图所示：

![[1702973160512-b5fa637d-ddda-4546-8063-2aab79426110.png]]

我们看到，我们刚刚增加的配置项已经存在了。
