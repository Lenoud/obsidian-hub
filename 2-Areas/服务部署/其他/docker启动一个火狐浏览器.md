# docker启动一个火狐浏览器

docker启动一个火狐浏览器相关技术笔记。

## 全新版本（推荐使用）
```yaml
version: '3.0'
services:
  firefox:
    container_name: firefox
    hostname: firefox
    shm_size: 3G
    image: jlesage/firefox:v23.11.3
    restart: always
    ports:
      - 5900:5900
      - 5800:5800
    cpus: 3
    mem_limit: 3G
    environment:
      TZ: Asia/Hong_Kong
      LANG: zh_CN.UTF-8
      DISPLAY_WIDTH: 1920
      DISPLAY_HEIGHT: 1080
      KEEP_APP_RUNNING: 1
      ENABLE_CJK_FONT: 1
      SECURE_CONNECTION: 1
      VNC_PASSWORD: "lb781023"
    volumes:
      - ./firefox-data:/config
      - ./language:/usr/share/fonts/
      - ./hosts:/etc/hosts
```

其它火狐容器

```bash
docker run -d --name firefox -p 8083:8083 -p 5900:5900 \
 -v /root/docker-file/firefox_dockerfile/Downloads:/root/Downloads \
 oldiy/firefox-novnc:latest

 #/root/docker-file/firefox_dockerfile/Downloads:/root/Downloads 下载文件存放的地址
 #vnc连接5900即可
```

[GitHub - jlesage/docker-firefox: Docker container for Firefox](https://github.com/jlesage/docker-firefox)

下载镜像

docker pull jlesage/firefox

运行容器

```bash
docker run -d --name=firefox -e TZ=Asia/Hong_Kong \
-p 5800:5800 -p 5900:5900 --shm-size 2g \
-e DISPLAY_WIDTH=1920 -e KEEP_APP_RUNNING=1 -e DISPLAY_HEIGHT=1080 -e ENABLE_CJK_FONT=1 -e VNC_PASSWORD=lb781023  \
-v /docker/firefox:/config:rw /firefox
```

```bash
version: '3.0'
services:
  firefox:
    shm_size: 1GB
    image: jlesage/firefox:latest
    restart: always
    ports:
      - 5900:5900
      - 5800:5800
    environment:
      TZ: Asia/Hong_Kong
      DISPLAY_WIDTH: 1920
      DISPLAY_HEIGHT: 1080
      KEEP_APP_RUNNING: 1
      ENABLE_CJK_FONT: 1
      SECURE_CONNECTION: 1
      VNC_PASSWORD: "lb781023"
    volumes:
      - /home/luobozi/Downloads:/config
```

这个镜像的源地址是官方alpine的源，ENABLE_CJK_FONT: 1这个参数下载语言包时有可能失败，/etc/cont-init.d/10-cjk-font.sh 下载语言包的脚本，我们需要改成阿里云的源或其它国内源

#进入容器执行

 sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/cont-init.d/10-cjk-font.sh

./etc/cont-init.d/10-cjk-font.sh

下载成功后重启容器即可

可以把这个文件复制出来，挂载，下一次就不用手动修改了

/home/luobozi/firefox-vnc/10-cjk-font.sh:/etc/cont-init.d/10-cjk-font.sh

或者是把容器的语言文件存放地址挂载出来，再手动修改执行一次脚本把文件下载到本地，也行

/home/luobozi/firefox-vnc/language:/usr/share/fonts/wqy-zenhei

参数解释：

-e TZ=Asia/Hong_Kong ##这个是设置地区。

-e DISPLAY_WIDTH=1920

-e DISPLAY_HEIGHT=1080 ##设置显示的高宽

-e KEEP_APP_RUNNING=1 ##关闭了之后会自动重启，不然所有标签页关闭了，浏览器也就关了。

-e ENABLE_CJK_FONT=1 ##一定要加这个，不然中文显示乱码

-e SECURE_CONNECTION=1 ##启用 HTTPS，再绑定域名，安全一点点

-e VNC_PASSWORD=xxxxxxxx ##访问密码，不然谁打开都能用了

-p 5800:5800 -p 5900:5900 ##端口映射

-v /www/firefox:/config:rw ##数据，包括下载的东西，不然不好找。
