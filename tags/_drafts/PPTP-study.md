# PPTP

PPTP uses an extended version of GRE(unhanced GRE) to carry user PPP packets.





#### GRE and Unhanced GRE

```

GRE header

    ---------------------------------
    |                               |
    |       Delivery Header         |
    |                               |
    ---------------------------------
    |                               |
    |       GRE Header              |
    |                               |
    ---------------------------------
    |                               |
    |       Payload packet          |
    |                               |
    ---------------------------------

Unhanced GRE header

    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |C|R|K|S|s|Recur|A| Flags | Ver |         Protocol Type         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |    Key (HW) Payload Length    |       Key (LW) Call ID        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                  Sequence Number (Optional)                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |               Acknowledgment Number (Optional)                |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

#### PPTP tunnel

A tunnel is defined by a PNS-PAC pair.  The tunnel protocol is
      defined by a modified version of GRE [[1](https://tools.ietf.org/html/rfc2637#ref-1 ""Generic Routing Encapsulation (GRE)""),[2](https://tools.ietf.org/html/rfc2637#ref-2 ""Generic Routing Encapsulation (GRE) over IPv4 Networks"")].  The tunnel carries
      PPP datagrams between the PAC and the PNS.  Many sessions are
      multiplexed on a single tunnel.  A control connection operating
      over TCP controls the establishment, release, and maintenance of
      sessions and of the tunnel itself.

There are two parallel components of PPTP: 

1) Control Connection
   between each PAC-PNS pair operating over TCP 

2) an IP tunnel
   operating between the same PAC-PNS pair which is used to transport
   GRE encapsulated PPP packets for user sessions between the pair.

#### Beginning

A control connection can be established by either the PNS or the PAC.
   Following the establishment of the required TCP connection, the PNS
   and PAC establish the control connection using the Start-Control-
   Connection-Request and -Reply messages.

在TCP链接成功后会发送 Start-Control-Connection-Request  &  Start-Control-Connection-Reply 同时会交换一些基础的信息。

初始化成功后会发送出站请求  Outgoing-Call-Request   &  Outgoing-Call-Reply

The control connection may communicate changes in
   operating characteristics of an individual user session with a Set-
   Link-Info message.

### 总结一下

PPTP会在Client和Server之间建立一条tunnel，然后在tunnel中使用GRE封装的PPP进行数据的传输。

#### Processing in kernel

linux-3.4.11/net/ipv4/gre.c

内核对GRE的处理比较简单。

```c
module_init(gre_init);


static int __init gre_init(void)
{
    inet_add_protocol(&net_gre_protocol, IPPROTO_GRE)
        ....
    return 0;
}
```

inet_add_protocol 这个函数实现在 net/ipv4/protocol.c 中。这里的哈希数组inet_protos存储着所有的inet 二层协议。

```c
int inet_add_protocol(const struct net_protocol *prot, unsigned char protocol)
{
    int hash = protocol & (MAX_INET_PROTOS - 1);

    return !cmpxchg((const struct net_protocol **)&inet_protos[hash],
            NULL, prot) ? 0 : -1;
}
EXPORT_SYMBOL(inet_add_protocol);
```

这里cmpxchg 函数意思是比较 前两个参数，如相同则将第三个参数存储到第一个参数中，[详细可见这里]([Linux内核中的cmpxchg函数_zdy0_2004的专栏-CSDN博客](https://blog.csdn.net/zdy0_2004/article/details/48013829))。 这样就调用到gre__rcv函数中。

```c
static const struct net_protocol net_gre_protocol = {
    .handler     = gre_rcv,
    .err_handler = gre_err,
    .netns_ok    = 1,
};


static int gre_rcv(struct sk_buff *skb)
{
    const struct gre_protocol *proto;
    u8 ver;
    int ret;
     ....
    ver = skb->data[1]&0x7f;        //获取 GRE version
    if (ver >= GREPROTO_MAX)
        goto drop;

    rcu_read_lock();
    proto = rcu_dereference(gre_proto[ver]);
    if (!proto || !proto->handler)
        goto drop_unlock;
    ret = proto->handler(skb);   // 关键一句
    ......
}
```

ret = proto->handler(skb);   这个句柄是什么比较关键了。既然是从gre_proto数组获得的协议，那么就看是谁填充了这个数组。

```








ip_route_input_common
ip_route_input_slow
ip_mkroute_input
__mkroute_input








/home/hert/studycode/linux-3.4.11/include/linux/netfilter/nf_conntrack_proto_gre.h

/* GRE Protocol field */
#define GRE_PROTOCOL_PPTP	0x880B




/home/hert/studycode/linux-3.4.11/net/netfilter/nf_conntrack_proto_gre.c +398
