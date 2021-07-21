### 为什么网络包里有了IP之后还需要MAC

1. 从历史的角度来说 先有MAC地址,再有IP地址， IP 地址是 TCP/IP协议提出来的
2. 从分层的角度来说 MAC 是数据链路层的地址 IP 是网络层的地址
3. 从路由的角度来说 IP地址本质上是终点地址 它在跳过路由器（hop）的时候不会改变，而MAC地址则是下一跳的地址，每跳过一次路由器都会改变。IP 有定位的功能， MAC 没有。



### 网络地址分类

- ABCDE分类 

  - 组成

    标志位 网络号 主机号 主机号的长度限制了主机数量 2 ** len(host) - 2

- 私有地址 公有地址

- CIDR 无类型域间选路

  - 组成

    网络号 主机号

### IP ADDR 信息

- lo loopback 环回接口 用于本机通讯

- eth0 网卡

- net_device flag 

  - BROADCAST 可以发送广播包
  - MULTICAST 可以发送多播包
  - LOWER_UP 网线插着

- MTU 最大传输单元 

  限制的是数据链路层的payload，也就是**上层协议**的大小，例如IP，ICMP等。

  超过会被丢弃或被分片。

- qdisc 排队规则 pfifo pfifo_fast 波段

- tos type of service 服务类型 决定在哪个



### 如何自动配置IP地址

DHCP 动态主机配置协议 (基于UDP协议) 报文包括 MAC 地址

- 步骤

  ![步骤](https://bkimg.cdn.bcebos.com/pic/5ab5c9ea15ce36d3d5e5e08939f33a87e850b1a1?x-bce-process=image/resize,m_lfit,w_640,limit_1/format,f_auto)

- 如何自动安装操作系统

  预启动执行环境 PXE (基于tftp)

  安装完成以后可以通过修改BIOS选项boot优先级来避免每次重启都安装操作系统





已知 IP 地址，求 MAC 地址的协议，为了避免每次都用 ARP 请求，机器本地也会进行 ARP 缓存。

