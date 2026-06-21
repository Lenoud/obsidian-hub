# cron表达式

cron表达式相关技术笔记。

```bash
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12)
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

| 执行时间 | 格式 |
| --- | --- |
| 每分钟定时执行一次 | * * * * * |
| 每小时定时执行一次 | 0 * * * * |
| 每天定时执行一次 | 0 0 * * * |
| 每周定时执行一次 | 0 0 * * 0 |
| 每月定时执行一次 | 0 0 1 * * |
| 每月最后一天定时执行一次 | 0 0 L * * |
| 每年定时执行一次 | 0 0 1 1 * |

```python
#每小时登录一次校园网，防止断线。
10 */1 * * * bash /home/liubiao/login.sh >> /home/liubiao/login.log 2>&1
11 */1 * * * /usr/bin/date >> /home/liubiao/login.log 2>&1

#开始判断公网ip是为严格模式nat。
11 */1 * * * bash /home/liubiao/get_ip.sh > /home/liubiao/shareIPdata 2>&1
12 */1 * * * /usr/bin/python /home/liubiao/showIP.py >> /home/liubiao/shareIP.log 2>&1
14 */1 * * * /usr/bin/cat /home/liubiao/shareIPdata >> /home/liubiao/shareIP.log 2>&1
15 */1 * * * /usr/bin/echo   >> /home/liubiao/shareIP.log 2>&1
16 */1 * * * /usr/bin/date >> /home/liubiao/shareIP.log 2>&1
17 */1 * * * /usr/bin/echo "----------Cron运行结束----------" >> /home/liubiao/shareIP.log 2>&1

#周日清理日志。
0 0 * * 0 /usr/bin/echo > /home/liubiao/login.log
0 0 * * 0 /usr/bin/echo > /home/liubiao/shareIP.log

```

### 实例
每一分钟执行一次 /bin/ls：

* * * * * /bin/ls

在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup：

0 6-12/3 * 12 * /usr/bin/backup

周一到周五每天下午 5:00 寄一封信给 alex@domain.name：

0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata

每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 echo "haha"：

20 0-23/2 * * * echo "haha"

下面再看看几个具体的例子：

0 */2 * * * /sbin/service httpd restart

意思是每两个小时重启一次apache

50 7 * * * /sbin/service sshd start

意思是每天7：50开启ssh服务

50 22 * * * /sbin/service sshd stop

意思是每天22：50关闭ssh服务

0 0 1,15 * * fsck /home

每月1号和15号检查/home 磁盘

1 * * * * /home/bruce/backup

每小时的第一分执行 /home/bruce/backup这个文件

00 03 * * 1-5 find /home -name "*.xxx" -mtime +4 -exec rm {} \;

每周一至周五3点钟，在目录/home中，查找文件名为*.xxx的文件，并删除4天前的文件。

30 6 */10 * * ls

意思是每月的1、11、21、31日的6：30执行一次ls命令

日志收集详见[Ubuntu 系统默认不生成 cron 日志文件](https://www.yuque.com/u33971054/atri/orikiguyx122st7t)

```bash
27 10 * * * /usr/bin/sh /opt/lyy/checkES.sh >>/opt/lyy/checkES.log 2>&1
```

**注意：**当程序在你所指定的时间执行后，系统会发一封邮件给当前的用户，显示该程序执行的内容，若是你不希望收到这样的邮件，请在每一行空一格之后加上 > /dev/null 2>&1 即可，如：

20 03 * * * . /etc/profile;/bin/sh /var/www/runoob/test.sh > /dev/null 2>&1
