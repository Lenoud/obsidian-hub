# 服务器配置文件

服务器配置文件相关技术笔记。
### 1. /etc/nginx
Nginx配置文件目录, 所有的Nginx配置文件都在这里.

### 2. /etc/nginx/nginx.conf
Nginx的主配置文件, 可以修改它来改变Nginx的全局配置.

### 3. /etc/nginx/sites-available
这个目录存储每一个网站的"server blocks", Nginx通常不会使用这些配置, 除非它们被链接到sites-enabled目录. 一般所有的server block配置都在这个目录中设置, 然后软链接到别的目录.

    - 在一些 Linux 发行版中，Nginx 的虚拟主机配置文件通常会存放在 sites-available 目录中。
    - 然后通过在 sites-enabled 目录中创建符号链接来启用特定的虚拟主机配置。

### 4. /etc/nginx/sites-enabled
这个目录存储生效的"server blocks"配置. 通常, 这个配置都是链接到sites-available目录中的配置文件.

### 5. /etc/nginx/snippets
这个目录主要可以包含在其它Nginx配置文件中的配置片段, 重复的配置都可以重构为配置片段.

# 日志文件
### 1. /var/log/nginx/access.log
每一个访问请求都会默认记录在这个文件中, 除非你做了其它设置.

### 2. /var/log/nginx/error.log
任何Nginx的错误信息都会记录到这个文件中.

# 网站根目录
### /usr/share/nginx/html/ (或者 /var/www/html/):
    - 这是默认的网站根目录，用于存放网站的静态文件、HTML 页面、图片等资源。
    - 当您在 Nginx 配置中指定了一个网站时，默认情况下该站点的根目录就是这里。
    -
