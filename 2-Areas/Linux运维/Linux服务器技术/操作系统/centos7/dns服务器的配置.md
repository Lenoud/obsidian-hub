# dns服务器的配置

数据库配置与权限管理。

软件包名称 &工具名称

bind & bind-utils

服务名称

named

几个主要的配置文件路径

/etc/named.conf

/etc/named.rfc1912.zones

/var/named/

```bash

zone "pancakeman.xyz" IN {
        type hint;
        file "pancakeman.xyz.zone";
};

```

```bash
zone "pancakeman.xyz" IN {
        type master;
        file "pancakeman.xyz.zone";
};

```

注意！

NS 记录指向了 pancakeman.xyz，但是没有相应的地址记录（A 或 AAAA 记录）。这会导致 DNS 服务器无法解析 pancakeman.xyz 域名

所以要添加

NS  www.pancakeman.xyz.

www     A       192.168.1.22

```bash
$TTL 1D
@       IN SOA  pancakeman.xyz. rname.invalid. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      www.pancakeman.xyz.
www     A       192.168.1.22
adminf0dmmpys3xrkojbafci967s9peoq       A       192.168.1.22
h5.zb360        A       192.168.1.22
api.ab360    A  192.168.1.22

```
