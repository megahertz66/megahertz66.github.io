# TCPDUMP 使用详细说明


## 参数说明

    -A  以ASCII形式打印数据包内容，去除各级链接级别的头部，方便捕获网页信息。
    
    -B  设置捕获的缓存空间大小，单位为Kb
    
    -c  指定捕获数据包的数量 

    -C  指定保存数据包到文件的文件大小，通过-w指定文件名称

    -d  将数据包内容转换为人类可读的形式打印至标准输出

    -dd 将数据报内容转换为C语言的形式

    -ddd将数据包内容转换为10进制数字的形式，带计数

    -D  输出当前系统中，可以通过tcpdump捕获数据包的接口信息，可能附带有对该接口的说明

    -e  输出链路头部信息

    -f  使用文件的内容作为数据包过滤的规则

    -h  帮助信息

    --version   版本信息
    
    -H 尝试检测802.11s网头

    -i  指定捕获数据包的网络接口，在2.2内核版本之后，可以使用 -i “any” 指定全部接口，但是需要root权限

    -K  不对TCP IP UDP进行和校验（弄清楚）

    -l  输出到标准输出

    -L 列出网络接口的已知数据链路

    -n  禁止将地址转换为名称

    -#  打印时同时给数据包加上编号

    -O  不使用数据包匹配规则时的优化器

    -q  快速输出，打印少量、较短的信息内容

    -Q  选择包的传输方向， -Q in、 out、 inout。该选项不适用所有的平台

    -r  指定从文件中读取数据包，一般为通过-w 或其他工具创建的 pcap 和 pcap-ng类型文件。

    -S  打印绝对序列号，而不是TCP的序列号

    -t  不打印时间戳

    -tt 每秒打印时间戳，从1970 1 1 00：00

    -ttt 打印两行之间的时间，微妙为单位

    -tttt 微秒从第一行开始打印间隔时间戳

    -U 指定数据包大小，超出的内容将不会被输出

    -v  打印解析数据包

    -vv 输出更多关于数据包的信息

    -vvv 输出更更多的信息

    -w  将原始数据包在打印之前写入文件中。

    -XX 以16进制和ASCII的形式输出捕获的数据包   


## 

使用tcpdump之前首次应该熟悉各种类型的报文。

注意在tcpdump 和 wireshark 协议的名称不同，比如在wireshark中ipv6 icmp协议为icmpv6，而在tcpdump中其为icmp6。


## 输出格式

默认情况下：
```s
#tcpdump -i any 

19:35:41.482548 IP 203.208.40.70.443 > hert-T440p.34033: UDP, length 44
19:35:41.508038 IP 203.208.40.70.443 > hert-T440p.34033: UDP, length 1350
19:35:41.508638 IP 203.208.40.70.443 > hert-T440p.34033: UDP, length 27
```
前面是当前的系统时间
IP
地址和端口号
数据包长度


加-e：
```s
#tcpdump -e -i en0s25

19:40:11.930952 68:ca:e4:e6:54:7e (oui Unknown) > 28:d2:44:40:b9:66 (oui Unknown), ethertype IPv4 (0x0800), length 88: SDC-AD02.SDC.SERCOMM.COM.domain > hert-T440p.55314: 46953 NXDomain 0/0/0 (46)
19:40:11.932853 28:d2:44:40:b9:66 (oui Unknown) > 68:ca:e4:e6:54:7e (oui Unknown), ethertype IPv4 (0x0800), length 87: hert-T440p.38396 > SDC-AD02.SDC.SERCOMM.COM.domain: 55160+ PTR? 206.142.21.172.in-addr.arpa. (45)
19:40:11.935919 68:ca:e4:e6:54:7e (oui Unknown) > 28:d2:44:40:b9:66 (oui Unknown), ethertype IPv4 (0x0800), length 87: SDC-AD02.SDC.SERCOMM.COM.domain > hert-T440p.38396: 55160 NXDomain 0/0/0 (45)
19:40:11.938023 28:d2:44:40:b9:66 (oui Unknown) > 68:ca:e4:e6:54:7e (oui Unknown), ethertype IPv4 (0x0800), length 86: hert-T440p.35926 > SDC-AD02.SDC.SERCOMM.COM.domain: 48192+ PTR? 21.142.21.172.in-addr.arpa. (44)
19:40:11.943542 68:ca:e4:e6:54:7e (oui Unknown) > 28:d2:44:40:b9:66 (oui Unknown), ethertype IPv4 (0x0800), length 86: SDC-AD02.SDC.SERCOMM.COM.domain > hert-T440p.35926: 48192 NXDomain 0/0/0 (44)
19:40:12.058703 00:21:d7:06:c9:0c (oui Unknown) > 01:00:0c:cc:cc:cd (oui Unknown), ethertype 802.1Q (0x8100), length 68: vlan 41, p 7, LLC, dsap SNAP (0xaa) Individual, ssap SNAP (0xaa) Command, ctrl 0x03: oui Cisco (0x00000c), pid PVST (0x010b), length 42: STP 802.1d, Config, Flags [none], bridge-id 8029.00:21:d7:06:c9:00.800c, length 42

```

```s
hert@hert-T440p:~$ sudo tcpdump -q -i enp0s25

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s25, link-type EN10MB (Ethernet), capture size 262144 bytes
19:43:07.596086 IP 122.226.191.211.https > hert-T440p.54902: tcp 0
19:43:07.597689 IP hert-T440p.53514 > SDC-AD02.SDC.SERCOMM.COM.domain: UDP, length 44
19:43:07.600852 IP SDC-AD02.SDC.SERCOMM.COM.domain > hert-T440p.53514: UDP, length 44
19:43:07.602206 IP hert-T440p.37620 > SDC-AD02.SDC.SERCOMM.COM.domain: UDP, length 46
19:43:07.617395 IP SDC-AD02.SDC.SERCOMM.COM.domain > hert-T440p.37620: UDP, length 46
19:43:08.437618 STP 802.1d, Config, Flags [none], bridge-id 8029.00:21:d7:06:c9:00.800c, length 42
19:43:09.235410 STP 802.1d, Config, Flags [none], bridge-id 808e.00:21:d7:06:c9:00.800c, length 42
```

```s
hert@hert-T440p:~$ sudo tcpdump -i enp0s25 'port 80'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s25, link-type EN10MB (Ethernet), capture size 262144 bytes
19:52:02.104176 IP hert-T440p.53078 > 104.22.10.214.http: Flags [.], ack 2111015245, win 501, length 0
19:52:02.249783 IP 104.22.10.214.http > hert-T440p.53078: Flags [.], ack 1, win 66, length 0
```

## 捕获各种网络协议

**NS & NA**
```s
hert@hert-T440p:~$ sudo tcpdump -i enp0s25 "ip6[40]==136 || ip6[40]==135"
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on enp0s25, link-type EN10MB (Ethernet), capture size 262144 bytes
14:59:09.730593 IP6 _gateway > ff02::1:ff75:a363: ICMP6, neighbor solicitation, who has fe80::7ce9:b2d:c075:a363, length 32
14:59:17.729825 IP6 _gateway > ff02::1:ffcd:679a: ICMP6, neighbor solicitation, who has hert-T440p, length 32
14:59:17.729893 IP6 hert-T440p > _gateway: ICMP6, neighbor advertisement, tgt is hert-T440p, length 32
14:59:49.723838 IP6 _gateway > ff02::1:ffcd:679a: ICMP6, neighbor solicitation, who has hert-T440p, length 32
14:59:49.723909 IP6 hert-T440p > _gateway: ICMP6, neighbor advertisement, tgt is hert-T440p, length 32
14:59:49.724003 IP6 _gateway > ff02::1:ff75:a363: ICMP6, neighbor solicitation, who has fe80::7ce9:b2d:c075:a363, length 32
```

-nvvv之后的变化

```s
hert@hert-T440p:~$ sudo tcpdump -i enp0s25 "ip6[40]==136 || ip6[40]==135" -nvvv
tcpdump: listening on enp0s25, link-type EN10MB (Ethernet), capture size 262144 bytes
15:02:29.696416 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::a4e:bfff:fe30:6448 > ff02::1:ffcd:679a: [icmp6 sum ok] ICMP6, neighbor solicitation, length 32, who has fe80::e89:5a83:fccd:679a
	  source link-address option (1), length 8 (1): 08:4e:bf:30:64:48
	    0x0000:  084e bf30 6448
15:02:29.696472 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::e89:5a83:fccd:679a > fe80::a4e:bfff:fe30:6448: [icmp6 sum ok] ICMP6, neighbor advertisement, length 32, tgt is fe80::e89:5a83:fccd:679a, Flags [solicited, override]
	  destination link-address option (2), length 8 (1): 28:d2:44:40:b9:66
	    0x0000:  28d2 4440 b966
15:02:29.697198 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::a4e:bfff:fe30:6448 > ff02::1:ff75:a363: [icmp6 sum ok] ICMP6, neighbor solicitation, length 32, who has fe80::7ce9:b2d:c075:a363
	  source link-address option (1), length 8 (1): 08:4e:bf:30:64:48
	    0x0000:  084e bf30 6448
15:03:01.690842 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::a4e:bfff:fe30:6448 > ff02::1:ffcd:679a: [icmp6 sum ok] ICMP6, neighbor solicitation, length 32, who has fe80::e89:5a83:fccd:679a
	  source link-address option (1), length 8 (1): 08:4e:bf:30:64:48
	    0x0000:  084e bf30 6448
15:03:01.690903 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::e89:5a83:fccd:679a > fe80::a4e:bfff:fe30:6448: [icmp6 sum ok] ICMP6, neighbor advertisement, length 32, tgt is fe80::e89:5a83:fccd:679a, Flags [solicited, override]
	  destination link-address option (2), length 8 (1): 28:d2:44:40:b9:66
	    0x0000:  28d2 4440 b966
```

加入 -XX 

```s
hert@hert-T440p:~$ sudo tcpdump -i enp0s25 "ip6[40]==136 || ip6[40]==135" -XX -nvvv
tcpdump: listening on enp0s25, link-type EN10MB (Ethernet), capture size 262144 bytes
15:06:50.316717 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::7ce9:b2d:c075:a363 > ff02::1:ff30:6448: [icmp6 sum ok] ICMP6, neighbor solicitation, length 32, who has fe80::a4e:bfff:fe30:6448
	  source link-address option (1), length 8 (1): 00:e0:4c:36:07:41
	    0x0000:  00e0 4c36 0741
	0x0000:  3333 ff30 6448 00e0 4c36 0741 86dd 6000  33.0dH..L6.A..`.
	0x0010:  0000 0020 3aff fe80 0000 0000 0000 7ce9  ....:.........|.
	0x0020:  0b2d c075 a363 ff02 0000 0000 0000 0000  .-.u.c..........
	0x0030:  0001 ff30 6448 8700 ab16 0000 0000 fe80  ...0dH..........
	0x0040:  0000 0000 0000 0a4e bfff fe30 6448 0101  .......N...0dH..
	0x0050:  00e0 4c36 0741                           ..L6.A
15:07:17.647780 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::a4e:bfff:fe30:6448 > ff02::1:ffcd:679a: [icmp6 sum ok] ICMP6, neighbor solicitation, length 32, who has fe80::e89:5a83:fccd:679a
	  source link-address option (1), length 8 (1): 08:4e:bf:30:64:48
	    0x0000:  084e bf30 6448
	0x0000:  3333 ffcd 679a 084e bf30 6448 86dd 6000  33..g..N.0dH..`.
	0x0010:  0000 0020 3aff fe80 0000 0000 0000 0a4e  ....:..........N
	0x0020:  bfff fe30 6448 ff02 0000 0000 0000 0000  ...0dH..........
	0x0030:  0001 ffcd 679a 8700 ee32 0000 0000 fe80  ....g....2......
	0x0040:  0000 0000 0000 0e89 5a83 fccd 679a 0101  ........Z...g...
	0x0050:  084e bf30 6448                           .N.0dH
15:07:17.647857 IP6 (hlim 255, next-header ICMPv6 (58) payload length: 32) fe80::e89:5a83:fccd:679a > fe80::a4e:bfff:fe30:6448: [icmp6 sum ok] ICMP6, neighbor advertisement, length 32, tgt is fe80::e89:5a83:fccd:679a, Flags [solicited, override]
	  destination link-address option (2), length 8 (1): 28:d2:44:40:b9:66
	    0x0000:  28d2 4440 b966
	0x0000:  084e bf30 6448 28d2 4440 b966 86dd 6000  .N.0dH(.D@.f..`.
	0x0010:  0000 0020 3aff fe80 0000 0000 0000 0e89  ....:...........
	0x0020:  5a83 fccd 679a fe80 0000 0000 0000 0a4e  Z...g..........N
	0x0030:  bfff fe30 6448 8800 2bf7 6000 0000 fe80  ...0dH..+.`.....
	0x0040:  0000 0000 0000 0e89 5a83 fccd 679a 0201  ........Z...g...
	0x0050:  28d2 4440 b966 
```

**RS & RA**

`hert@hert-T440p:~$ sudo tcpdump -i enp0s25 "ip6[40]==133 || ip6[40]==134"`

