# sport&dport解释

简单的说sport就是我们访问他人，dport是他人访问我们

```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT #这条规则代表他人能访问我们的80端口
iptables -A INPUT -p tcp --sport 80 -j ACCEPT #这条规则代表我们能访问他人80端口
```

---

以前一直对iptables的sport、dport不清楚，所以这里记录一下。

（1）清理防火墙：

iptables -F iptables -X iptables -Z

（2）**iptables命令选项输入顺序：**

iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网> --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作

表名包括：

+ **raw**：高级功能，如：网址过滤。
+ **mangle**：数据包修改（QOS），用于实现服务质量。
+ **net**：地址转换，用于网关路由器。
+ **filter**：包过滤，用于防火墙规则。

规则链名包括：

+ **INPUT链**：处理输入数据包。
+ **OUTPUT链**：处理输出数据包。
+ **FORWARD链**：处理转发数据包。
+ **PREROUTING链**：用于目标地址转换（DNAT），路由前。
+ **POSTOUTING链**：用于源地址转换（SNAT），路由后。
+ ![[1684890184884-9531ed48-6010-42e2-a4d4-16b3120dfa63.png]]

动作包括：

+ [accept](http://man.linuxde.net/accept)：接收数据包。
+ **DROP**：丢弃数据包。
+ **REDIRECT**：重定向、映射、透明代理。
+ **SNAT**：源地址转换。
+ **DNAT**：目标地址转换。
+ **MASQUERADE**：IP伪装（NAT），用于ADSL。
+ **LOG**：日志记录。

iptables里面的dport和sport

首先先来翻译一下dport和sport的意思：

dport：目的端口
sport：来源端口
初学iptables比较容易迷糊，但是我尽量用通俗的语言给你讲解。

dport 和sport字面意思来说很好理解，一个是数据要到达的目的端口，一个是数据来源的端口。

但是在使用的时候要分具体情况来对待，这个具体情况就是你的数据包的流动行为方式。（INPUT还是OUTPUT）

比如你的例子：iptables -A INPUT -p tcp --dport 80 -j ACCEPT
注意里面的INPUT参数，这个代表你的这条数据包的进行的 “进入” 操作！
那么你的这条数据包可以这么描述：
1.这是一条从外部进入内部本地服务器的数据。
2.数据包的目的（dport）地址是80，就是要访问我本地的80端口。
3.允许以上的数据行为通过。
总和：允许外部数据访问我的本地服务器80端口。

再看第2条列子：iptables -A INPUT -p tcp --sport 80 -j ACCEPT
1.这是一条从外部进入内部本地服务器的数据。
2.数据包的来源端口是（sport）80，就是对方的数据包是80端口发送过来的。
3.允许以上数据行为。
总结：允许外部的来自80端口的数据访问我的本地服务器。

input方式总结： dport指本地，sport指外部。

如果你的数据包是（OUTPUT）行为，那么就是另外一种理解方式：
比如：
iptables -A OUTPUT -p tcp –dport 80 -j ACCEPT
1.这是一条从内部出去的数据。
2.出去的目的（dport）端口是80。
3.允许以上数据行为。

output行为总结：dport指外部，sport指本地。
