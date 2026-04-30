# 注册为脚本为systemctl管理

注册为systemctl管理

```bash
#编写注册服务配置文件
root@ubuntu:/home/liubiao/nps_service# cat /etc/systemd/system/npc-server.service
 [Unit]
 Description=npc-server
 After=network-online.target firewalld.service
 [Service]
 Type=forking
 ExecStart=/bin/bash -c "/usr/local/bin/npc-server.sh &"
 [Install]
 WantedBy=multi-user.target

#bash -c的作用是将一个长字符串当做一条完整的命令来执行
#如果在脚本路径后面加上后台运行符号（&），sh脚本就会在后台运行
#不会一直处于挂起状态，systemd也就不会一直等待sh文件执行完成了。
#经过测试，完全符合预期。

#重载配置并设置开机自启
root@ubuntu:/home/liubiao/nps_service# systemctl daemon-reload
root@ubuntu:/home/liubiao/nps_service# systemctl enable npc-server

#启动
root@ubuntu:/home/liubiao/nps_service# systemctl start npc-server
root@ubuntu:/home/liubiao/nps_service# systemctl status npc-server
● npc-server.service - npc-server
     Loaded: loaded (/etc/systemd/system/npc-server.service; enabled; vendor preset: enabled)
     Active: activating (start) since Thu 2023-06-22 13:52:44 CST; 25s ago
Cntrl PID: 81997 (npc-server.sh)
      Tasks: 10 (limit: 9437)
     Memory: 4.9M
        CPU: 40ms
     CGroup: /system.slice/npc-server.service
             ├─81997 /bin/bash /usr/local/bin/npc-server.sh
             └─81998 ./home/liubiao/nps_service/npc -server=139.9.44.244:8024 -vkey=8xejfu246l3bb5uf -type=tcp

6月 22 13:52:44 ubuntu systemd[1]: Starting npc-server...
6月 22 13:52:44 ubuntu npc-server.sh[81998]: 2023/06/22 13:52:44.545 [I] [npc.go:231]  the version of client is 0.26.10, the core version of client is 0.26.0
6月 22 13:52:44 ubuntu npc-server.sh[81998]: 2023/06/22 13:52:44.592 [I] [client.go:72]  Successful connection with server 139.9.44.244:8024
root@ubuntu:/home/liubiao/nps_service#
root@ubuntu:/home/liubiao/nps_service# ls
conf  log  npc  open  start.sh
root@ubuntu:/home/liubiao/nps_service# pwd
/home/liubiao/nps_service
root@ubuntu:/home/liubiao/nps_service# cat /usr/local/bin/
npc-server.sh  todesk
root@ubuntu:/home/liubiao/nps_service# cat /usr/local/bin/npc-server.sh
#!/bin/bash
./home/liubiao/nps_service/npc -server=139.9.44.244:8024 -vkey=8xejfu246l3bb5uf -type=tcp

```
