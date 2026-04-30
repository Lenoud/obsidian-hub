# Ubuntu 系统默认不生成 cron 日志文件

Ubuntu 系统默认不生成 cron 日志文件

解决方法:

    在终端输入以下命令👇

```bash
sudo vim /etc/rsyslog.d/50-default.conf
# 然后找到 cron.* ，把前面的 # 去掉，保存退出。
```

    重启系统日志，在终端输入命令：

```bash
sudo service rsyslog restart
```

稍等片刻进入 /var/log/ 即可看到 cron.log 文件，这个文件就是 crontab 的日志文件。如果还没出现该文件，可以试试重启 cron 服务：

```bash
sudo service cron restart
```

1、如何查看crontab定时任务是否执行

（1）查看crontab的日志

日志文件为/var/log/cron

找到对应时间，是否执行指令

这种方式只能看到是否执行，但是并无法确定是否执行成功

（2）将定时任务的日志重定向

日志重定向的时候要注意，要将标准错误日志一起重定向，才能获取到正常和错误的日志

例如：

```bash
27 10 * * * /usr/bin/sh /opt/lyy/checkES.sh >>/opt/lyy/checkES.log 2>&1
```
