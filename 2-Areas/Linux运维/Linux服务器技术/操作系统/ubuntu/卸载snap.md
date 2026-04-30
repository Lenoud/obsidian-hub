# 卸载snap

Ubuntu 系统卸载 snap 包管理器。

[https://sysin.cn/blog/ubuntu-remove-snap/](https://sysin.cn/blog/ubuntu-remove-snap/)

```yaml
for p in $(snap list | awk '{print $1}'); do
  sudo snap remove $p
done

#再次执行，提示如下，表明已经删除干净：
# No snaps are installed yet. Try 'snap install hello-world'.

sudo systemctl stop snapd
sudo systemctl disable --now snapd.socket

for m in /snap/core/*; do
   sudo umount $m
done

sudo apt autoremove --purge snapd

rm -rf ~/snap
sudo rm -rf /snap
sudo rm -rf /var/snap
sudo rm -rf /var/lib/snapd
sudo rm -rf /var/cache/snapd

#配置 APT 参数：禁止 apt 安装 snapd。
sudo sh -c "cat > /etc/apt/preferences.d/no-snapd.pref" << EOL
Package: snapd
Pin: release a=*
Pin-Priority: -10
EOL
```
