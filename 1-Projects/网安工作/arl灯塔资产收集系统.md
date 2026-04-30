# ARL 灯塔资产收集系统

ARL（Asset Reconnaissance Lighthouse）是 TophantTechnology 开源的资产侦察灯塔系统，用于信息收集和资产发现。

官方文档：https://tophanttechnology.github.io/ARL-doc/faq/

## Docker 部署

```bash
cd /opt/
mkdir docker_arl
wget -O docker_arl/docker.zip https://github.com/TophantTechnology/ARL/releases/download/v2.6.2/docker.zip
cd docker_arl
unzip -o docker.zip
docker volume create arl_db
docker compose pull
docker compose up -d
```

## 忘记密码

```bash
docker exec -ti arl_mongodb mongo -u admin -p admin
use arl
db.user.drop()
db.user.insert({ username: 'admin',  password: hex_md5('arlsalt!@#'+'admin123') })
```

## 源码安装

```bash
wget https://raw.githubusercontent.com/TophantTechnology/ARL/master/misc/setup-arl.sh
chmod +x setup-arl.sh
./setup-arl.sh
```
