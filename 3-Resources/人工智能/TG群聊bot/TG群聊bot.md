# TG群聊bot

TG群聊bot相关技术笔记。

## 安装宝塔
使用宝塔安装 mysql5.7、redis

数据库创建一个用户名、库名、密码全部一致的数据库

## 论坛地址
#luobozi/123456  论坛[https://www.97bot.com/](https://www.97bot.com/)

## 添加一个机器人

```yaml
使用tg账号添加好友
getuseridsbot
@BotFather
发送消息获取token
Use this token to access the HTTP API:
6550557894:AAEFMV7kPFhyitmyl8RknqNjHHnzF1vzaDk

萝卜子, [2024/1/16 14:06]
/newbot

BotFather, [2024/1/16 14:06]
Alright, a new bot. How are we going to call it? Please choose a name for your bot.

萝卜子, [2024/1/16 14:06]
tgbotNo1

BotFather, [2024/1/16 14:06]
Good. Now let's choose a username for your bot. It must end in `bot`. Like this, for example: TetrisBot or tetris_bot.

萝卜子, [2024/1/16 14:07]
tgNo1bot

BotFather, [2024/1/16 14:07]
Done! Congratulations on your new bot. You will find it at t.me/tgNo1bot. You can now add a description, about section and profile picture for your bot, see /help for a list of commands. By the way, when you've finished creating your cool bot, ping our Bot Support if you want a better username for it. Just make sure the bot is fully operational before you do this.

Use this token to access the HTTP API:
6550557894:AAEFMV7kPFhyitmyl8RknqNjHHnzF1vzaDk
Keep your token secure and store it safely, it can be used by anyone to control your bot.

For a description of the Bot API, see this page: https://core.telegram.org/bots/api
```

## .env配置文件详解
```bash
#■■■■■■■■■■■■■■■■MYSQL数据库配置■■■■■■■■■■■■■■■■#

#使用云数据库? 0本地数据库 1云数据库(不懂就保持默认不用改)
OtherS  = 0

#(必填)数据库地址(不懂就保持默认不用改)
DB_HOST = 127.0.0.1

#(必填)数据库端口(不懂就保持默认不用改)
DB_PORT = 3306

#(必填)数据库
DB_NAME = ttt

#(必填)数据库用户名
DB_USER = ttt

#(必填)数据库密码
DB_PASSWORD = ttt

#(必填) Telegram 开发者api_id和api_hash
api_id   = 18933685
api_hash = 9d77a8874aead39b76a5d21303908260
#访问这个网址申请https://my.telegram.org/auth
#输入手机号后，它会在tg账号消息中发一条消息代码

#(必填) 波场区块浏览器 apikey
TRONSCAN_APIKEY = 1b3408ee-06ce-433d-98f5-956e70a52f69
#https://tronscan.org/ 注册账号创建apikey

#(必填) 波场节点官方   apikey
tronGrid_APIkey = deba43a8-2d2d-42bc-a24b-d6b6c5a76642
#访问这个网址注册账号，随后注册一个apikey、https://www.trongrid.io/register

#(选填)TON钱包助词器(自动开会员业务模块需要) 部署时可以暂时不填 注意助词器在''符号内
TON_Private_Key = ''

#教程：https://www.bori99.com/28397.html

```
