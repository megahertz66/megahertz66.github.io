inet6_init D:\studycode\linux-3.4.11\net\ipv6\af_inet6.c

icmpv6_init();

static int icmpv6_rcv(struct sk_buff *skb)    D:\studycode\linux-3.4.11\net\ipv6\icmp.c

ndisc_rcv(skb); D:\studycode\linux-3.4.11\net\ipv6\ndisc.c

ndisc_router_discovery(skb);

ndisc_parse_options(opt, optlen, &ndopts)

static inline int ndisc_is_useropt(struct nd_opt_hdr *opt)
{
	return opt->nd_opt_type == ND_OPT_RDNSS;
}



rtnl_notify(skb, net, 0, RTNLGRP_ND_USEROPT, NULL, GFP_ATOMIC);

void rtnl_notify(struct sk_buff *skb, struct net *net, u32 pid, u32 group,
		 struct nlmsghdr *nlh, gfp_t flags)

netlink 是什么？怎么用



