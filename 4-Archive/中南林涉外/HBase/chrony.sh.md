## master
```bash
#!/bin/bash
echo -e "\e[33m您确定要继续执行这个操作吗？ (yes/no)\e[0m"
read -r control

if [[ "$control" == "no" ]]; then
    echo -e "\e[31m操作已取消。\e[0m"
    exit 1
fi
echo -e "\e[32m继续执行操作...\e[0m"

# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
  echo "请以 root 用户运行此脚本"
  exit 1
fi

# 备份原始配置文件
cp /etc/chrony.conf /etc/chrony.conf.bak

# 注释掉所有的 pool 行
sed -i 's/^pool/#pool/g' /etc/chrony.conf

# 配置新的 NTP 服务器
cat >> /etc/chrony.conf << EOF
server ntp1.aliyun.com iburst
server ntp2.aliyun.com iburst
server ntp3.aliyun.com iburst
server ntp4.aliyun.com iburst

allow all
EOF

# 重启 Chrony 服务并检查 NTP 源
systemctl restart chronyd

sleep 1s

chronyc sources -v

```

## node
```bash
#!/bin/bash

# 确保脚本以 root 用户权限运行
if [ "$(id -u)" -ne "0" ]; then
  echo "请以 root 用户运行此脚本"
  exit 1
fi

# 备份原始配置文件
cp /etc/chrony.conf /etc/chrony.conf.bak

# 注释掉所有的 pool 行
sed -i 's/^pool/#pool/g' /etc/chrony.conf

# 配置新的 NTP 服务器并允许所有网络
cat >> /etc/chrony.conf << EOF
server HBase01 iburst

EOF

# 重启 Chrony 服务并检查 NTP 源
systemctl restart chronyd

sleep 1s

chronyc sources -v

```
