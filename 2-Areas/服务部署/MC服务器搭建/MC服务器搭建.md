# MC服务器搭建

MC服务器搭建相关技术笔记。

1.18.1 服务器 server.jar包下载地址

[https://www.minecraft.net/zh-hans/article/minecraft-java-edition-1-18-1](https://www.minecraft.net/zh-hans/article/minecraft-java-edition-1-18-1)

mod服务器下载

[https://new.mohistmc.com/](https://new.mohistmc.com/)

模组下载

[https://www.mcmod.cn/](https://www.mcmod.cn/)

[https://www.curseforge.com/](https://www.curseforge.com/)

使用docker搭建java17环境运行服务器

```yaml
FROM ubuntu
ADD jdk-17_linux-x64_bin.tar.gz /
RUN mv /jdk-17.0.7 /usr/local/jdk-17
RUN mkdir /Server_MC
WORKDIR /Server_MC
ENV JAVA_HOME=/usr/local/jdk-17
ENV PATH $PATH:$JAVA_HOME/bin
EXPOSE 25565
```

```bash

docker run -it -p 25565:25565 -v /home/luobozi/java17/server181:/Server_MC --name mc-server jdk-17/mc:v1.0 java -Xmx1G -Xms1G -XX:+UseCompressedOops -jar server.jar nogui
#jar包放在/home/luobozi/java17/server181下

luobozi@hecs-22820:~/java17$ ls server181/
banned-ips.json  banned-players.json  crash-reports  eula.txt  libraries  logs  ops.json  server.jar  server.properties  usercache.json  versions  whitelist.json  world

sed -i s/eula=false/eula=true/g eula.txt
```

## Minecraft服务器属性
### Java版
| 属性 | 类型 | 默认值 | 描述 |
| :---: | --- | --- | --- |
| **allow-flight** | 布尔值 | false | 允许玩家在安装添加飞行功能的[mod](https://minecraft.fandom.com/zh/wiki/Mod)前提下在生存模式下飞行。
允许飞行可能会使[恶意破坏](https://minecraft.fandom.com/zh/wiki/%E6%95%99%E7%A8%8B/%E9%98%B2%E6%AD%A2%E6%81%B6%E6%84%8F%E7%A0%B4%E5%9D%8F)者更加常见，因为此设定会使他们更容易达成目的。在创造模式下无作用。
**false** - 不允许飞行。悬空超过5秒的玩家会被踢出服务器。 |
| **allow-nether** | 布尔值 | true | 允许玩家进入[下界](https://minecraft.fandom.com/zh/wiki/%E4%B8%8B%E7%95%8C)。
**false** - [下界传送门](https://minecraft.fandom.com/zh/wiki/%E4%B8%8B%E7%95%8C%E4%BC%A0%E9%80%81%E9%97%A8)不会生效。 |
| **broadcast-console-to-ops** | 布尔值 | true | 向所有[在线OP](https://minecraft.fandom.com/zh/wiki/%E6%9C%8D%E5%8A%A1%E5%99%A8#%E7%AE%A1%E7%90%86%E5%92%8C%E7%BB%B4%E6%8A%A4%E6%9C%8D%E5%8A%A1%E5%99%A8)
发送所执行命令的输出。 |
| **broadcast-rcon-to-ops** | 布尔值 | true | 向所有在线OP发送通过RCON执行的命令的输出。 |
| **difficulty** | 字符串 | easy | 定义服务器的游戏[难度](https://minecraft.fandom.com/zh/wiki/%E9%9A%BE%E5%BA%A6)
（例如生物对玩家造成的伤害，饥饿和中毒对玩家的影响方式等）。
如果设置了旧的数字ID，则会自动转化为英文的难度名称。
**peaceful** (0) - 和平 |
| enable-command-block | 布尔值 | false | 是否启用命令方块。 |
| **enable-jmx-monitoring** | 布尔值 | false | 暴露一个具有对象名net.minecraft.server:type=Server的MBean和两个属性averageTickTime和tickTimes用于暴露以毫秒为单位的tick时间。
为了启用JRE的JMX，你需要添加[在此处所述](https://docs.oracle.com/javase/8/docs/technotes/guides/management/agent.html)的一些JVM标志。 |
| **enable-query** | 布尔值 | false | 允许使用GameSpy4协议的服务器监听器。用于获取服务器信息。 |
| **enable-rcon** | 布尔值 | false | 是否允许远程访问服务器控制台。
+ 由于RCON协议传输数据时没有加密，所以不建议把RCON暴露在互联网上。RCON客户端和服务端交换的所有数据（包括RCON密码）都会泄露给正在监听此连接的人。 |
| **enable-status** | 布尔值 | true | 使服务器在服务器列表中看起来是“在线”的。 |
| **enforce-secure-profile** | 布尔值 | true | 要求玩家必须具有Mojang签名的公钥才能进入服务器。
**true** - 不具有Mojang签名的公钥的玩家不能进入服务器。 |
| **enforce-whitelist** | 布尔值 | false | 在服务器上强制执行白名单。
当启用后，不在白名单（前提是启用）中的用户将在服务器重新加载白名单文件后从服务器踢出。
**true** - 不在白名单上的用户会被踢出。 |
| **entity-broadcast-range-percentage** | 整数（10-1000） | 100 | 此选项控制实体需要距离玩家有多近才会将数据包发送给客户端。更高的数值意味着实体可以在更远的地方就被渲染，同时也可能提高增加延迟的概率。
这个值是以默认值的百分比来表示的。例如：将此值设为50，表示将渲染正常情况下一半距离以内的生物。 |
| **force-gamemode** | 布尔值 | false | 强制玩家加入时为默认[游戏模式](https://minecraft.fandom.com/zh/wiki/%E6%B8%B8%E6%88%8F%E6%A8%A1%E5%BC%8F)。
**false** - 玩家将以退出前的游戏模式加入 |
| **function-permission-level** | 整数（1-4） | 2 | 设定[函数](https://minecraft.fandom.com/zh/wiki/Java%E7%89%88%E5%87%BD%E6%95%B0)
的默认权限等级。
4个等级的详情见 [#op-permission-level](https://minecraft.fandom.com/zh/wiki/Server.properties#op-permission-level)。 |
| **gamemode** | 字符串 | survival | 定义默认[游戏模式](https://minecraft.fandom.com/zh/wiki/%E6%B8%B8%E6%88%8F%E6%A8%A1%E5%BC%8F)
如果值是旧用的数字，会静默转换为对应游戏模式的英文名称。
**survival** (0) - 生存模式 |
| **generate-structures** | 布尔值 | true | 定义是否能生成[结构](https://minecraft.fandom.com/zh/wiki/%E7%BB%93%E6%9E%84)
（例如村庄）。
**false** - 新生成的区块中将不包含结构。
**注：**即使设为false，地牢仍然会生成。 |
| **generator-settings** | 字符串 | {} | 本属性质用于自定义世界的生成。详见[超平坦世界](https://minecraft.fandom.com/zh/wiki/%E8%B6%85%E5%B9%B3%E5%9D%A6%E4%B8%96%E7%95%8C)
和[自定义](https://minecraft.fandom.com/zh/wiki/%E8%87%AA%E5%AE%9A%E4%B9%89)
了解正确的设定及例子。 |
| **hardcore** | 布尔值 | false | 如果设为 **true**，服务器难度的设置会被忽略并且设为 hard（困难），玩家在死后会自动切换至旁观模式。 |
| **hide-online-players** | 布尔值 | false | 如果设为 **true**，服务端在响应客户端状态请求时不会返回在线玩家列表。 |
| **initial-disabled-packs** | 字符串 | _空白_ | 需要在创建世界过程中禁用的数据包名称，以逗号分隔。 |
| **initial-enabled-packs** | 字符串 | vanilla | 需要在创建世界过程中启用的数据包名称，以逗号分隔。特别地，功能数据包必须在此指定才能生效。 |
| **level-name** | 字符串 | world | “level-name”的值将作为世界名称及其文件夹名。你也可以把你已生成的世界存档复制过来，然后让这个值与那个文件夹的名字保持一致，服务器就可以载入该存档。
部分字符，例如 ' （单引号）可能需要在前面加反斜杠号 \ 才能被正常应用。 |
| **level-seed** | 字符串 | _空白_ | 与单人游戏类似，为你的世界定义一个[种子](https://minecraft.fandom.com/zh/wiki/%E7%A7%8D%E5%AD%90%EF%BC%88%E4%B8%96%E7%95%8C%E7%94%9F%E6%88%90%EF%BC%89)。
这里有一些例子：minecraft，404，1a2b3c。 |
| **level-type** | 字符串 | minecraft:normal | 使用世界预设ID，确定地图所生成的类型。
使用世界预设ID时，需要在其中的“:”前加“\”转义。原版世界预设ID可以省略其前面的“minecraft:”（即命名空间）。
**minecraft:normal** - 带有丘陵，河谷，海洋等的标准的世界。 |
| **max-build-height** | 整数 | 256 | 玩家在游戏中能够建造的最大高度。可能会在该值较小时生成超过该值的地形。 |
| **max-chained-neighbor-updates** | 整数[[需要更多信息](https://minecraft.fandom.com/zh/wiki/Talk:Server.properties)
] | 1000000 | 限制连锁NC更新的数量，超过此数量的连锁NC更新会被跳过。若为负数则无限制。 |
| **max-players** | 整数（0-2147483647） | 20 | 服务器同时能容纳的最大玩家数量。请注意，在线玩家越多，对服务器造成的负担也就越大。同样注意，服务器的OP具有在人满的情况下强行进入服务器的能力：找到在服务器根目录下叫ops.json的文件并打开，将需要此能力的OP下的bypassesPlayerLimit选项设置为true即可（默认值为false），这意味着OP将不需要在服务器人满时等待有玩家离开后再加入。过大的数值会使客户端显示的玩家列表崩坏。 |
| **max-tick-time**    | 整数（0–(2^63 - 1)） | 60000 | 设置每个tick花费的最大毫秒数。超过该毫秒数时，服务器看门狗将停止服务器程序并附带上信息：服务器的一个tick花费了60.00秒（最长也应该只有0.05秒）；判定服务器已崩溃，它将被强制关闭。遇到这种情况的时候，它会调用 System.exit(1)。
译者注：如果你监测服务程序的返回代码，此时返回代码会为1。（习惯上，程序正常退出应当返回0）
**-1** - 完全停用看门狗（这个停用选项在 14w32a 快照中添加） |
| **max-world-size**    | 整数（1-29999984） | 29999984 | 设置可让[世界边界](https://minecraft.fandom.com/zh/wiki/%E4%B8%96%E7%95%8C%E8%BE%B9%E7%95%8C)
获得的最大半径值，单位为方块。通过成功执行的命令能把世界边界设置得更大，但不会超过这里设置的最大方块限制。如果设置的 max-world-size 超过默认值的大小，那将不会起任何效果。
例如：
+ 设置 max-world-size为1000将会有2000×2000的地图边界。
+ 设置 max-world-size为4000将会有8000×8000的地图边界。 |
| **motd** | 字符串 | A Minecraft Server | 本属性值是玩家客户端的多人游戏服务器列表中显示的服务器信息，显示于名称下方。
+ MOTD 支持[样式代码](https://minecraft.fandom.com/zh/wiki/%E6%A0%B7%E5%BC%8F%E4%BB%A3%E7%A0%81)。
+ MOTD 支持特殊符号，比如"♥"。然而，这些符号需要转换为Unicode转义字符。你可以在[这里](http://www.freeformatter.com/string-utilities.html#charinfo)找到一个转换器。
+ 如果MOTD超过59个字符，服务器列表很可能会返回“通讯错误”。 |
| **network-compression-threshold**    | 整数 | 256 | 默认会允许n-1字节的数据包正常发送, 如果数据包为n字节或更大时会进行压缩。所以，更低的数值会使得更多的数据包被压缩，但是如果被压缩的数据包字节太小将反而使压缩后字节更大。
**-1** - 完全禁用数据包压缩
**注：**以太网规范要求把小于64字节的数据包填充为64字节。因此，设置一个低于64的值可能没有什么好处。也不推荐让设置的值超过MTU（通常为1500字节）。 |
| **online-mode** | 布尔值 | true | 是否让服务器对比Minecraft账户数据库验证登录信息。只有在你的服务器**并未**与 Internet 连接时，才将这个值设为false。如果设为false，黑客就能够使用任意假账户连接服务器！如果minecraft.net服务器宕机或不可访问，那么该值设为true的服务器会因为无法验证玩家身份而拒绝所有玩家加入。通常，这个值设为true的服务器被称为“正版服务器”。故意设定该变量为false的服务器称为“破解服务器”（也称离线服务器），这类服务器允许拥有未授权的[Minecraft](https://minecraft.fandom.com/zh/wiki/Minecraft)
副本的玩家加入。
**true** - 启用。服务器会认为自己具有 Internet 连接，并检查每一位连入的玩家。 |
| **op-permission-level** | 整数（1-4） | 4 | 设定使用/[op](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/op)
命令时OP的权限等级。所有存档会从之前的存档继承能力和命令。
**1** - OP可以绕过重生点保护。 |
| **player-idle-timeout** | 整数 | 0 | 如果不为0，服务器将在玩家的空闲时间达到设置的时间（单位为分钟）时将玩家踢出服务器
**注：**当服务器接受到下列数据包之一时将会重置空闲时间：
+ [点击窗口](http://wiki.vg/Protocol#Click_Window)
+ [附魔物品](http://wiki.vg/Protocol#Enchant_Item)
+ [更新告示牌](http://wiki.vg/Protocol#Update_Sign)
+ [玩家挖掘方块](http://wiki.vg/Protocol#Player_Digging)
+ [玩家放置方块](http://wiki.vg/Protocol#Player_Block_Placement)
+ [更换拿着的物品](http://wiki.vg/Protocol#Held_Item_Change_.28serverbound.29)
+ [动画](http://wiki.vg/Protocol#Animation_.28serverbound.29)（挥动手臂）
+ [实体动作](http://wiki.vg/Protocol#Entity_Action)
+ [客户端状态](http://wiki.vg/Protocol#Client_Status)
+ [聊天信息](http://wiki.vg/Protocol#Chat_Message_.28serverbound.29)
+ [使用实体](http://wiki.vg/Protocol#Use_Entity) |
| **prevent-proxy-connections** | 布尔值 | false | 如果服务器发送的ISP/AS和Mojang的验证服务器的不一样，玩家将会被踢出。
**true** - 启用。服务器将会禁止玩家使用虚拟专用网络或代理。 |
| **pvp** | 布尔值 | true | 是否允许PvP。也只有在允许PvP时玩家自己的箭才会受到伤害。
**true** - 玩家可以互相残杀。
**注：**由玩家造成的间接伤害（例如[熔岩](https://minecraft.fandom.com/zh/wiki/%E7%86%94%E5%B2%A9)，[火](https://minecraft.fandom.com/zh/wiki/%E7%81%AB)，[TNT](https://minecraft.fandom.com/zh/wiki/TNT)等，某种程度上还有[水](https://minecraft.fandom.com/zh/wiki/%E6%B0%B4)，[沙子](https://minecraft.fandom.com/zh/wiki/%E6%B2%99%E5%AD%90)和[沙砾](https://minecraft.fandom.com/zh/wiki/%E6%B2%99%E7%A0%BE)）还是会伤害其他玩家。 |
| **query.port** | 整数（1-65534） | 25565 | 设置监听服务器的端口号（参见 **enable-query**）。 |
| **rate-limit** | 整数 | 0 | 设置玩家被踢出服务器前，可以发送的数据包数量。
设置为0表示关闭此功能。 |
| **rcon.password** | 字符串 | _空白_ | 设置RCON远程访问的密码（参见**enable-rcon**）。RCON：能允许其他应用程序通过互联网与Minecraft服务器连接并交互的远程控制台协议。 |
| **rcon.port** | 整数（1-65534) | 25575 | 设置RCON远程访问的端口号。 |
| **require-resource-pack** | 布尔值 | false | 当此选项启用（设为true）时，玩家会被提示作出选择（是否启用服务器资源包）。如果玩家拒绝则会被服务器断开连接。 |
| **resource-pack** | 字符串 | _空白_ | 可选选项，可输入指向一个[资源包](https://minecraft.fandom.com/zh/wiki/%E8%B5%84%E6%BA%90%E5%8C%85)的URI。玩家可选择是否使用该资源包。
注意若该值含":"和"="字符，需要在其前加上反斜线(\)，例如 http\://somedomain.com/somepack.zip?someparam\=somevalue 资源包大小理应不能超过
+ 50 MiB（1.15-pre5前）
+ 100 MiB（1.15-pre5到1.18-pre8）
+ 250 MiB（1.18-rc1起）
注意，下载成功或失败由客户端记录，而非服务器。 |
| **resource-pack-prompt** | 字符串 | _空白_ | 可选，用于在使用require-resource-pack时在资源包提示界面显示自定义信息。
与聊天组件语法一致，可以包含多行文本。 |
| **resource-pack-sha1** | 字符串 | _空白_ | 资源包的SHA-1值，必须为小写十六进制，建议填写它。这还没有用于验证资源包的完整性，但是它提高了资源包缓存的有效性和可靠性。 |
| **server-ip** | 字符串 | _空白_ | 将服务器与一个特定IP绑定。强烈建议留空该属性值！
留空，或是填入你想让服务器绑定（监听）的IP。 |
| **server-port** | 整数（1-65534） | 25565 | 改变服务器（监听的）端口号。如果服务器在使用[NAT](https://zh.wikipedia.org/wiki/%E7%BD%91%E7%BB%9C%E5%9C%B0%E5%9D%80%E8%BD%AC%E6%8D%A2)
的网络中运行，该端口必须被[转发](https://zh.wikipedia.org/wiki/%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91)
（在你有家用路由器/防火墙的前提下）。 |
| **simulation-distance** | 整数（3-32） | 10 | 设置服务端可更新实体范围的最大值，即玩家各个方向上的区块数量（是以玩家为中心的半径，不是直径）。超出此范围的实体不会被更新，对玩家也不可见。
默认/推荐设置为10，如果有严重卡顿的话，减少该数值。 |
| **spawn-animals** | 布尔值 | true | 决定[动物](https://minecraft.fandom.com/zh/wiki/%E5%8A%A8%E7%89%A9)
是否可以生成。
**true** - 动物可以正常生成。
提示：如果你有严重的卡顿，可以设为false。 |
| **spawn-monsters** | 布尔值 | true | 决定攻击型生物（怪物）是否可以生成。
**true** - 启用。怪物会生成于夜晚和黑暗处。
如果difficulty=0（即难度设置为和平）的话，该属性值不会有任何影响。
提示：如果你有严重的卡顿，可以设为false。 |
| **spawn-npcs** | 布尔值 | true | 决定是否生成[村民](https://minecraft.fandom.com/zh/wiki/%E6%9D%91%E6%B0%91)
。
**true** - 启用。生成村民。 |
| **spawn-protection** | 整数 | 16 | 通过将该值进行2x+1的运算来决定出生点的保护半径。设置为1会保护以出生点为中心的3×3方块的区域，2会保护5×5方块的区域，3会保护7×7方块的区域，以此类推。这个选项不在第一次服务器启动时生成，只会在第一个玩家加入服务器时出现。如果服务器没有设置OP，这个选项会自动禁用。
设置为0将不会禁用出生点保护，但会保护位于出生点的那一个方块（13w05a前）。 |
| **sync-chunk-writes** | 布尔值 | true | 启用后区块文件以同步模式写入。 |
| **text-filtering-config** | [[需要更多信息](https://minecraft.fandom.com/zh/wiki/Talk:Server.properties)
] | _空白_ | [[需要更多信息](https://minecraft.fandom.com/zh/wiki/Talk:Server.properties)
] |
| **use-native-transport** | 布尔值 | true | 是否使用针对Linux平台的数据包收发优化。此选项仅会在Linux平台上生成。
**true** - 启用。启用Linux数据包收发优化。 |
| **view-distance** | 整数（3-32） | 10 | 设置服务端发送给客户端的世界数据量，也就是设置玩家各个方向上的区块数量（是以玩家为中心的半径，不是直径）。它决定了服务端的可视距离。（另见[渲染距离](https://minecraft.fandom.com/zh/wiki/%E6%B8%B2%E6%9F%93%E8%B7%9D%E7%A6%BB)
）
默认/推荐设置为10，如果有严重卡顿的话，减少该数值。 |
| **white-list** | 布尔值 | false | 启用服务器的白名单。
当启用时，只有白名单上的用户才能连接服务器。白名单主要用于私人服务器，例如提供给相识的朋友、通过应用流程谨慎选择的陌生人等。
**false** - 不使用白名单。
_**注:**_ OP会自动被视为在白名单上，所以无需再将OP加入白名单。 |

**true** - 允许飞行。玩家得以使用任何能飞行的mod飞行。

**true** - 玩家可以通过下界[传送门](https://minecraft.fandom.com/zh/wiki/%E4%BC%A0%E9%80%81%E9%97%A8)前往下界。

**easy** (1) - 简单

**normal** (2) - 普通

**hard** (3) - 困难

**false** - 不具有Mojang签名的公钥的玩家也可进入服务器。

**false** - 不在白名单上的在线用户不会被踢出。

此功能模仿了客户端视频设置中的功能，而不像客户端的渲染距离设置一样只能在服务器设置的限制下调整渲染距离。

**true** - 玩家总是以默认游戏模式加入

**creative** (1) - 创造模式

**adventure** (2) - 冒险模式

**spectator** (3) - 旁观模式

**true** - 新生成的区块中将包含结构。

**minecraft:flat** - 一个没有特性的平坦世界，可用**generator-settings**修改。

**minecraft:large_biomes** - 如同预设（default）世界，但所有生物群系都更大。

**minecraft:amplified** - 如同预设世界，但世界生成高度提高。

**minecraft:single_biome_surface** - 单一生物群系世界，可用**generator-settings**修改。

**0** - 压缩全部数据包

**false** - 禁用。服务器不会尝试检查玩家。

**2** - OP可以使用所有单人游戏作弊命令（除了/[publish](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/publish)，因为不能在服务器上使用；/[debug](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/debug)也是）并使用命令方块。命令方块和领域服服主/管理员有此等级权限。

**3** - OP可以使用大多数多人游戏中独有的命令，包括 /[debug](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/debug)，以及管理玩家的命令（/[ban](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/ban)，/[op](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/op)等等）。

**4** - OP可以使用所有命令，包括 /[stop](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/stop), /[save-all](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/save-all), /[save-on](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/save-on) 和 /[save-off](https://minecraft.fandom.com/zh/wiki/%E5%91%BD%E4%BB%A4/save-off)。

**false** - 禁用。服务器将不会禁止玩家使用虚拟专用网络或代理。

**false** - 玩家无法互相造成伤害（也称作**玩家对战环境**（**PvE**））。

**false** - 动物生成后会立即消失。

**false** - 禁用。不会有任何怪物。

**false** - 禁用。不会有村民。

设置为0会禁用出生点保护（13w05a起，参见[MC-666](https://bugs.mojang.com/browse/MC-666)）。

**false** - 禁用。禁用Linux数据包收发优化。

**true** - 从whitelist.json文件加载白名单。
