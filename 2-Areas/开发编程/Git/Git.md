# Git 常用命令速查

Git 版本控制系统的常用命令和工作流程参考。

git init

使用我们指定目录作为Git仓库。

git init newrepo

初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

git add README

git commit -m '初始化项目版本'

_**注：**__ __在 Linux 系统中，commit 信息使用单引号__ __**'**__，Windows 系统，commit 信息使用双引号__ __**"**__。_

_所以在 git bash 中 __**git commit -m '提交说明'**__ 这样是可以的，在 Windows 命令行中就要使用双引号 __**git commit -m "提交说明"**__。_

| 命令 | 说明 |
| --- | --- |
| [git add](https://www.runoob.com/git/git-add.html) | 添加文件到暂存区 |
| [git status](https://www.runoob.com/git/git-status.html) | 查看仓库当前的状态，显示有变更的文件。 |
| [git diff](https://www.runoob.com/git/git-diff.html) | 比较文件的不同，即暂存区和工作区的差异。 |
| [git commit](https://www.runoob.com/git/git-commit.html) | 提交暂存区到本地仓库。 |
| [git reset](https://www.runoob.com/git/git-reset.html) | 回退版本。 |
| [git rm](https://www.runoob.com/git/git-rm.html) | 将文件从暂存区和工作区中删除。 |
| [git mv](https://www.runoob.com/git/git-mv.html) | 移动或重命名工作区文件。 |

```bash
#连接仓库并取别名为 origin
git remote add origin http://192.168.100.110:3000/liubiao/demo-2.git

#更新仓库文件
git add . #更新所有文件
git commit -m "file push"
git push -u origin master #推送到master分支

#删除远程仓库 origin
git remote rm origin

git push --set-upstream origin drone
#在这个命令中，drone 是你要推送的本地分支的名称，而 origin 是远程仓库的名称。
#通常，origin 是默认的远程仓库名称，它指向你克隆或添加的远程仓库。

#检查github的连通性
ssh -T git@github.com
#Hi Lenoud! You've successfully authenticated, but GitHub does not provide shell access.

```

```bash
#git branch [分支名称]: 创建一个新分支，但不会自动切换到该分支。例如，要创建一个名为 "feature-branch" 的新分支，可以执行：
git branch #查询
git branch feature-branch #create
git checkout feature-branch #移动
git switch -c bug-fix #创建斌且移动 git checkout -b bug-fix
git branch -d feature-branch #删除
git branch -m new-feature  #rename
```

+ **工作区：**就是你在电脑里能看到的目录。
+ **暂存区：**英文叫 stage 或 index。一般存放在 **.git** 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
+ **版本库：**工作区有一个隐藏目录 **.git**，这个不算工作区，而是 Git 的版本库。

```bash
git init
git add README.md
git commit -m "first commit"
git remote add origin http://192.168.100.110:3000/liubiao/demo.git
git push -u origin master

git init - 初始化仓库。
git add . - 添加文件到暂存区。
git commit - 将暂存区内容添加到仓库中。

   add        添加文件内容至索引
   bisect     通过二分查找定位引入 bug 的变更
   branch     列出、创建或删除分支
   checkout   检出一个分支或路径到工作区
   clone      克隆一个版本库到一个新目录
   commit     记录变更到版本库
   diff       显示提交之间、提交和工作区之间等的差异
   fetch      从另外一个版本库下载对象和引用
   grep       输出和模式匹配的行
   init       创建一个空的 Git 版本库或重新初始化一个已存在的版本库
   log        显示提交日志
   merge      合并两个或更多开发历史
   mv         移动或重命名一个文件、目录或符号链接
   pull       获取并合并另外的版本库或一个本地分支
   push       更新远程引用和相关的对象
   rebase     本地提交转移至更新后的上游分支中
   reset      重置当前HEAD到指定状态
   rm         从工作区和索引中删除文件
   show       显示各种类型的对象
   status     显示工作区状态
   tag        创建、列出、删除或校验一个GPG签名的 tag 对象

```

### ssh 访问 gitHub 出错如下：
```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (140.82.118.4)' can t be established. RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8. Are you sure you want to continue connecting (yes/no)?  Host key verification failed.
#解决办法：（将GitHub添加到信任主机列表后，可以成功访问）
$ ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
# github.com:22 SSH-2.0-babeld-d45c1532
$ ssh -T git@github.com
Warning: Permanently added the RSA host key for IP address '140.82.118.4' to the list of known hosts. Hi earthnorth! You've successfully authenticated, but GitHub does not provide shell access.
```

## 相关笔记
- [[Git配置文件详解]]
- [[MOC-开发编程]]
