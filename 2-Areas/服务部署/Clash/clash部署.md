# 1.Linux-压缩包
部署了v2后

获取到加密后的vmess值

在线转换订阅连接：[https://sub.nerocats.cn/](https://sub.nerocats.cn/)

将clash-for-linux.zip传入Linux主机

```shell
unzip clash-for-linux.zip
cd clash-for-linux

cat .env #写入订阅的地址
# Clash 订阅地址
export CLASH_URL='https://sub.nerocats.cn/sub?target=clash&url=vmess%3A%2F%2FeyJ2IjoyLCJwcyI6IjIzM2JveS10Y3AtMTU0LjIxLjg3LjkwIiwiYWRkIjoiMTU0LjIxLjg3LjkwIiwicG9ydCI6IjEyMzU3IiwiaWQiOiJmMDllYzM5Ni00NzFlLTRkNmQtYTFkYy0zZGZmZGM4NTEzZTIiLCJhaWQiOiIwIiwibmV0IjoidGNwIiwidHlwZSI6Im5vbmUiLCJwYXRoIjoiIn0%3D&insert=false&config=https%3A%2F%2Fraw.githubusercontent.com%2FACL4SSR%2FACL4SSR%2Fmaster%2FClash%2Fconfig%2FACL4SSR_Online.ini'
export CLASH_SECRET=''

#运行脚本
chmod +x *.sh
sudo ./start.sh

#开启代理
source /etc/profile.d/clash.sh
proxy_on
```

```shell
# 开启系统代理
function proxy_on() {
        export http_proxy=http://127.0.0.1:7890
        export https_proxy=http://127.0.0.1:7890
        export no_proxy=127.0.0.1,localhost
        export HTTP_PROXY=http://127.0.0.1:7890
        export HTTPS_PROXY=http://127.0.0.1:7890
        export NO_PROXY=127.0.0.1,localhost
        echo -e "\033[32m[√] 已开启代理\033[0m"
}

# 关闭系统代理
function proxy_off(){
        unset http_proxy
        unset https_proxy
        unset no_proxy
        unset HTTP_PROXY
        unset HTTPS_PROXY
        unset NO_PROXY
        echo -e "\033[31m[×] 已关闭代理\033[0m"
}

```

### systemd管理
```bash
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target
[Service]
Type=simple
# Restart=always
ExecStart=/home/luobozi/environment/clash-for-linux-master/bin/clash-linux-amd64 -d /home/luobozi/environment/clash-for-linux-master/conf
[Install]
WantedBy=multi-user.target

```

### 桌面图标
```bash
[Desktop Entry]
Name=Clash
Comment=Clash for Linux
Exec=/home/luobozi/environment/clash-for-linux-master/bin/clash-linux-amd64 -d /home/luobozi/environment/clash-for-linux-master/conf
Icon=/home/luobozi/environment/clash-for-linux-master/clash.png
Terminal=false
Type=Application
Categories=Network;
```

# 2.docker
```yaml
version: '3.0'
services:
  clash:
    image: dreamacro/clash:v1.18.0
    container_name: clash
    restart: unless-stopped
    ports:
      - "9090:9090"
      - "7890:7890"
      - "7891:7891"
      - "7892:7892"
    volumes:
      - "./dashboard/:/root/dashboard/"
      - "./conf/config.yaml:/root/.config/clash/config.yaml"
      - "/etc/localtime:/etc/localtime:ro"
    networks:
      clash_bridge:
        ipv4_address: 172.30.0.2
    logging:
      options:
        max-size: "1m"

networks:
  clash_bridge:
    driver: bridge
    ipam:
      config:
        - subnet: 172.30.0.0/16

```

```yaml
port: 7890
socks-port: 7891
allow-lan: true
mode: Rule
log-level: info
external-controller: :9090
# RESTful API 的口令
secret: lb123456
#ui路径
external-ui: /root/dashboard
proxies:
  - {name: 233boy-tcp-8.217.186.171, server: 8.217.186.171, port: 38811, type: vmess, uuid: 437833af-b462-4aee-88c7-f27ae1cb972d, alterId: 0, cipher: auto, tls: false}
#.......
```

# 3.wiki: clash
[https://github.com/vernesong/OpenClash/wiki](https://github.com/vernesong/OpenClash/wiki)
