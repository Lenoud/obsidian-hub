# TCP 三次握手与四次挥手

TCP 是面向连接的可靠传输协议,建立和断开连接时的握手流程是网络面试的核心考点。

> 手写笔记图:`![[1739627703935-750caac0-8620-4f7a-8dea-f8d88088c14d.jpeg]]`,下面是文字版要点。

## 三次握手(建立连接)

```text
Client                                  Server
  |                                       |
  |  ---- SYN, seq=x ----->               |  LISTEN
  |                                       |
  |  <--- SYN+ACK, seq=y, ack=x+1 -----   |  SYN_RCVD
  |                                       |
  |  ---- ACK, ack=y+1 ----->             |  ESTABLISHED
  |                                       |
  ESTABLISHED                             ESTABLISHED
```

| 步骤 | 方向 | 标志位 | 含义 |
| --- | --- | --- | --- |
| 1 | Client → Server | `SYN` | 客户端发起连接,seq=x |
| 2 | Server → Client | `SYN + ACK` | 服务端确认并同步,seq=y,ack=x+1 |
| 3 | Client → Server | `ACK` | 客户端确认,ack=y+1,连接建立 |

**为什么是三次,不是两次?**

- 两次握手无法确认"客户端的接收能力"。假设客户端发了一个旧 SYN 延迟到达,服务端回 SYN+ACK 就建立连接,但客户端没发起新连接,服务端就白等。
- 三次握手本质是"双方都确认对方的发送和接收能力"。

## 四次挥手(断开连接)

```text
Client                                  Server
  |                                       |
  |  ---- FIN, seq=u ----->               |  FIN_WAIT_1
  |                                       |
  |  <--- ACK, ack=u+1 -----              |  CLOSE_WAIT
  |                                       |
  |  (服务端可能还有数据要发)              |
  |  <--- 数据 ----                       |
  |  ---- ACK ----->                      |
  |                                       |
  |  <--- FIN, seq=v -----                |  LAST_ACK
  |                                       |
  |  ---- ACK, ack=v+1 ----->             |  CLOSED
  |                                       |
  TIME_WAIT(2MSL)                         CLOSED
```

| 步骤 | 含义 |
| --- | --- |
| 1 | 主动方发 FIN,表示自己不再发数据(但还能收) |
| 2 | 被动方回 ACK,进入 CLOSE_WAIT(可能继续发剩余数据) |
| 3 | 被动方数据发完后,发 FIN |
| 4 | 主动方回 ACK,进入 TIME_WAIT,等待 2MSL 后真正关闭 |

**为什么是四次,不是三次?**

TCP 是**全双工**,关闭需要两个方向分别关。被动方收到 FIN 时可能还有数据没发完,所以 ACK 和 FIN 不能合并(三次握手能合并 SYN+ACK 是因为此时服务端还没数据要发)。

**为什么主动方要 TIME_WAIT 2MSL?**

- 保证最后一个 ACK 能到达被动方(如果丢失,被动方会重发 FIN)
- 让旧连接的报文都消失,防止干扰新连接

## 11 种状态机(简化)

| 状态 | 说明 |
| --- | --- |
| `LISTEN` | 服务端等待连接 |
| `SYN_SENT` | 客户端发了 SYN,等待 ACK |
| `SYN_RCVD` | 服务端收到 SYN,发了 SYN+ACK |
| `ESTABLISHED` | 连接建立 |
| `FIN_WAIT_1` | 主动方发了 FIN |
| `FIN_WAIT_2` | 主动方收到 ACK |
| `CLOSE_WAIT` | 被动方收到 FIN,等应用 close() |
| `LAST_ACK` | 被动方发了 FIN,等最后一个 ACK |
| `TIME_WAIT` | 主动方发了最后的 ACK,等 2MSL |
| `CLOSING` | 双方同时关闭(少见) |
| `CLOSED` | 完全关闭 |

## 常用排查命令

```bash
# 查看所有 TCP 连接及状态
ss -tan | awk '{print $1}' | sort | uniq -c
#   LISTEN 5
#   ESTAB  20
#   TIME-WAIT  150

# 查看 TIME_WAIT 数量(高并发场景常见瓶颈)
ss -tan state time-wait | wc -l

# 抓包分析握手
tcpdump -i any -n 'tcp port 80 and (tcp[tcpflags] & tcp-syn != 0 or tcp[tcpflags] & tcp-fin != 0)'
```

## 常见问题

| 问题 | 答案 |
| --- | --- |
| 三次握手失败,服务端 SYN_RCVD 很多? | 客户端不发 ACK(攻击或网络差),调小 `tcp_synack_retries` |
| TIME_WAIT 太多怎么办? | 短连接频繁,改长连接;启用 `tcp_tw_reuse=1`(客户端)、`SO_REUSEADDR` |
| 如何避免 TIME_WAIT 占用端口? | 由主动方产生,通常让服务端主动关(改 keepalive) |
| SYN Flood 攻击怎么防? | 启用 `tcp_syncookies=1` |

## 相关笔记
- [[网络模型]] — OSI/TCP-IP 模型
- [[MOC-网络技术]]
- [[MOC-面试题]]
