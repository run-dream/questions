### 如何查看本机IP地址

```bash
# windows
ipconfig
# linux
ifconfig
ip addr 
# 区别 net-tools 和 iproute2
```

iproute2 支持的选项

```bash
ip { link | addr | addrlabel | route | rule | neigh | ntable | tunnel | maddr | mroute | mrule | monitor | xfrm | token }
```

### 如何配置IP地址

```bash
### net-tools
ifconfig eth1 %ip%/%网络号位数%
ifconfig eth1 up 

### iproute2
ip addr add %ip%/%网络号位数% dev eth1
ip link set up eth1
```



