x86-agent包

```powershell
cat > agent_install.sh << EOF
#!/bin/bash
if [ -f agent_local.tar ]; then
    echo -e "\033[1;32m文件 agent_local.tar 存在\033[0m"
else
    echo -e "\033[1;31m文件 agent_local.tar 不存在\033[0m"
    exit 0
fi

ip a|grep inet |grep -E eth\|en
read -p "输入本机IP地址: " IP
nvidia-smi 2> /dev/null || echo -e "\033[1;31m未安装GPU驱动\033[0m"
nvidia-docker 2> /dev/null || echo -e "\033[1;31m未安装GPU-docker驱动\033[0m"
yum -y install python3-pip || apt -y install python3-pip

mkdir agent_local
tar -zxvf agent_local.tar -C agent_local
cd ./agent_local
python3 -m venv agent_test
cd ./images
ls |xargs -i docker load -i {}
cd ..
cp ./tools/docker-compose-Linux-x86_64  /usr/local/bin/docker-compose
sed -i "s/10.100.5.251/${IP}/g" ./docker-compose.yaml
sed -i "s/47.98.98.74/${IP}/g" ./docker-compose.yaml
sed -i "s/60.165.238.65/${IP}/g" ./docker-compose.yaml
docker-compose up -d

echo -e "\033[1;32m服务启动完成，状态如下：\033[0m"

docker-compose  ps
EOF
```
