# xrdp连接黑屏问题

解决 xrdp 远程桌面连接 Ubuntu 时黑屏的问题。

## 连接黑屏问题
这个问题，**主要是当你的本机没有注销的话，远程桌面就会黑屏**，最佳解决策略就是退出本地登录，也就是注销登录，这个方法一定没问题。与windows那种完美的远程控制不同，在ubuntu中，本地登录和远程登陆是隔离开的，远程登录了不注销，那么本地就会黑屏，反过来本地登陆了不注销，远程就会黑屏。所谓注销就是logout，应该都懂，就是和关机、重启放在一起的那个选项。

或者使用网上的一些解决方案，但是这个放在在Ubuntu 22中会导致闪退。即，编辑 /etc/xrdp/startwm.sh 文件：

### 1. 打开文件

```bash
sudo vim /etc/xrdp/startwm.sh
```

### 2. 添加配置

```bash
unset DBUS_SESSION_BUS_ADDRESS
unset XDG_RUNTIME_DIR
```

### 3. 重启xrdp服务

```bash
sudo systemctl restart xrdp.service
```

## 桌面优化
注意，一定要**先修改下面配置文件，再远程连接**，否则会黑屏，这个时候需要重启。

反正记住一句话，重启后不在本地登录，那么远程必不黑屏！

如果不做任何配置，启动之后的桌面是非常别扭的，因为是Gnome的原始桌面，没有左侧的任务栏，窗口也没有最小化按钮，等等一些列问题。解决方案也很简单：

### 1. 添加配置文件

```bash
vim ~/.xsessionrc

添加：

export GNOME_SHELL_SESSION_MODE=ubuntu
export XDG_CURRENT_DESKTOP=ubuntu:GNOME
export XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
```

### 2. 重启xrdp服务

```bash
sudo systemctl restart xrdp.service
```
