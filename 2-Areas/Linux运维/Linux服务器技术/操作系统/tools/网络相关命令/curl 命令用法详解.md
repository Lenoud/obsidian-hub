# curl 命令用法详解

curl 命令的用法详解，支持多种协议的数据传输。

```shell
-d, --data <data>
#主要是针对 http 协议的 post 请求指定要在消息体中发送的数据。

-i, --include
#显示包含 http 消息头返回的信息输出。

-I, --header
#只获取 http 协议的消息头信息
#若是作用在 FTP 协议时，只获取文件大小和最后一次修改时间信息。

-H, --header <header>
#主要是针对 http 协议的消息头进行设置，常用的有 -H "Content-Type:application/json"

-o, --output <file>
#将服务器的响应保存成文件，等同于 wget 命令。

-X, --request <command>
#指定 http 协议的请求方式，默认为 GET 请求。
```

## 常用实例
### curl 发送 json 数据的 post 请求

利用 _-X_、_-H_ 和 _-d_ 参数进行 post 请求，示例如下：

```shell
curl -X POST -H "Content-Type:application/json" -d '{"msg_type":"text","content":{"text":"新更新提醒"}}' https://xxxx.xxxxx.xxx/notify
```

### curl 命令将响应返回保存成文件
利用 -o 参数可以将接口返回的数据直接保存成指定的文件，示例如下：

```shell
curl -X GET -o result.txt http://www.baidu.com
```

返回结果中包含响应头 -i

```shell
curl -X HEAD -i http://x.x.x.x:xx/xxx/xxxx
#和下面等同
curl --include http://x.x.x.x:xx/xxx/xxxx
```

### 只返回 header
```shell
curl -X HEAD -I http://x.x.x.x:xx/xxx/xxxx
curl -X HEAD --head http://x.x.x.x:xx/xxx/xxxx
```
