### ICMP

是互联网控制报文协议。ICMP 报文是封装在 IP 包里面的。

- 查询报文   

  ICMP ECHO REQUEST 

  ICMP ECHO REPLY 

  标识符 序号

- 差错报文

  终点不可达

  - 主机不可达
  - 端口不可达
  - 网络不可达
  - 协议不可达
  - 需要进行分片但设置了不分片位

  源站抑制

  时间超时 TTL

  路由重定向



### ping是怎么实现的

使用了 ICMP查询报文里面的 ECHO REQUEST 和 ECHO REPLY 类型 + 发送时间



### ping不通的时候怎么办

telnet 通过其他协议来测试网络是否通



### traceroute 怎么实现

使用了 ICMP差错报文，故意构造失败的ICMP

- 故意设置特殊的 TTL发送UDP包，加上不可能的值作为 UDP （大于 30000）端口号，利用是否返回端口不可达，来追踪去往目的地时沿途经过的路由器 
- 故意设置不分片，从而确定路径的 MTU