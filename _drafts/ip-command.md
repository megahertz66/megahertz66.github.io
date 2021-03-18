## ip

查看网卡信息
ip -s -s link ls eth0

开启和关闭设备
ip link set dev ethx up/down

为某个网卡配置ip
ip addr add xx dev ethx

显示网卡的ip地址
ip addr show ethx

改变mtu的值
ip link set dev eth0 mtu xxxx

修改mac地址
ip link set dev eth0 address xx:xx:xx:xx:xx:xx

清除网卡ip地址
ip addr flush ethx

配置默认路由
ip route add default via xx.xx.xx.xx [dev ethx]

删除默认路由
ip route del default via xx.xx.xx.xx [dev ethx]

给某个网段配置路由
ip route add xxxx/yy via zz dev ethx

删除某个网关的路由
ip route del xxxx/yy via zz dev ethx

给某个主机配置路由
ip route add x via y dev eth0

删除某个主机的路由
ip route add x via y dev eth0



## netstat

显示网络连接，路由表，接口状态，伪装连接，网络链路信息和组播成员组
参数
-a 显示所有监听的端口
-s 显示所有协议的统计数
-r 显示内核路由信息
-i  显示网络接口列表

显示网络接口列表
netstat -i

显示所有协议的统计值
netstat -s

显示内核路由表
netstat -r

查看tcp端口状态
netstat -tap

## 丢包

	1.丢的是什么类型的报文。
	2.在什么地方丢的报文。
	3.为什么会丢包。

硬件网卡丢包
ethtool -S ethx

链路层（二层丢包）
ifconfig

ip层（三层）
netstat -s查看报文统计

udp层（四层）
netstat -s 查看报文统计

tcp层（四层）
netstat -s eth0查看报文统计

ethtool -k ethx
	tcp-segmentation-offload: on
	udp-fragmentation-offload: on


## arping
	--在指定网卡上发送ARP请求指定地址，源地址 “-s” 参数指定，可用来直接 ping MAC 地址，以及找出那些 ip 地址被哪些电脑所使用了
参数
	-b 保持广播
	-f 得到第一个回复就 退出
	-q 不显示警告信息
	-U 主动的ARP模式，更新邻居
	-c <数据包的数目> 发送的数据包的数目
	-w <超时时间> 设置超时时间
	-I <网卡> 使用指定的以太网设备，默认情况下使用eth0
	-s 指定源IP地址
	-h 显示帮助信息
	-V 显示版本信息

