---
title: -NET- sk_buff Structure
date: 2021-02-18
categories:
- Linux
tags:
- Linux-networking
---




All the code defined in the `<include/linux/skbuff.h>` include file.

```c
/** 
 *	struct sk_buff - socket buffer
 *	@next: Next buffer in list
 *	@prev: Previous buffer in list
 *	@tstamp: Time we arrived
 *	@sk: Socket we are owned by
 *	@dev: Device we arrived on/are leaving by
 *	@cb: Control buffer. Free for use by every layer. Put private vars here
 *	@_skb_refdst: destination entry (with norefcount bit)
 *	@sp: the security path, used for xfrm
 *	@len: Length of actual data
 *	@data_len: Data length
 *	@mac_len: Length of link layer header
 *	@hdr_len: writable header length of cloned skb
 *	@csum: Checksum (must include start/offset pair)
 *	@csum_start: Offset from skb->head where checksumming should start
 *	@csum_offset: Offset from csum_start where checksum should be stored
 *	@priority: Packet queueing priority
 *	@local_df: allow local fragmentation
 *	@cloned: Head may be cloned (check refcnt to be sure)
 *	@ip_summed: Driver fed us an IP checksum
 *	@nohdr: Payload reference only, must not modify header
 *	@nfctinfo: Relationship of this skb to the connection
 *	@pkt_type: Packet class
 *	@fclone: skbuff clone status
 *	@ipvs_property: skbuff is owned by ipvs
 *	@peeked: this packet has been seen already, so stats have been
 *		done for it, don't do them again
 *	@nf_trace: netfilter packet trace flag
 *	@protocol: Packet protocol from driver
 *	@destructor: Destruct function
 *	@nfct: Associated connection, if any
 *	@nfct_reasm: netfilter conntrack re-assembly pointer
 *	@nf_bridge: Saved data about a bridged frame - see br_netfilter.c
 *	@skb_iif: ifindex of device we arrived on
 *	@tc_index: Traffic control index
 *	@tc_verd: traffic control verdict
 *	@rxhash: the packet hash computed on receive
 *	@queue_mapping: Queue mapping for multiqueue devices
 *	@ndisc_nodetype: router type (from link layer)
 *	@ooo_okay: allow the mapping of a socket to a queue to be changed
 *	@l4_rxhash: indicate rxhash is a canonical 4-tuple hash over transport
 *		ports.
 *	@wifi_acked_valid: wifi_acked was set
 *	@wifi_acked: whether frame was acked on wifi or not
 *	@no_fcs:  Request NIC to treat last 4 bytes as Ethernet FCS
 *	@dma_cookie: a cookie to one of several possible DMA operations
 *		done by skb DMA functions
 *	@secmark: security marking
 *	@mark: Generic packet mark
 *	@dropcount: total number of sk_receive_queue overflows
 *	@vlan_tci: vlan tag control information
 *	@transport_header: Transport layer header
 *	@network_header: Network layer header
 *	@mac_header: Link layer header
 *	@tail: Tail pointer
 *	@end: End pointer
 *	@head: Head of buffer
 *	@data: Data head pointer
 *	@truesize: Buffer size
 *	@users: User count - see {datagram,tcp}.c
 */

struct sk_buff {
	/* These two members must be first. */
	struct sk_buff		*next;
	struct sk_buff		*prev;

	ktime_t			tstamp;     // 到达时间

	struct sock		*sk;
	struct net_device	*dev;   // 接收或到达的设备

	/*
	 * This is the control buffer. It is free to use for every
	 * layer. Please put your private variables there. If you
	 * want to keep them across layers you have to do a skb_clone()
	 * first. This is owned by whoever has the skb queued ATM.
	 */
	char			cb[48] __aligned(8);

	unsigned long		_skb_refdst;
#ifdef CONFIG_XFRM
	struct	sec_path	*sp;
#endif
	unsigned int		len,    // 实际数据长度
				data_len;       // 数据长度
	__u16			mac_len,    // 链路头部长度
				hdr_len;        // 克隆的skb的可写标头长度 ???
	union {
		__wsum		csum;       // 校验和
		struct {
			__u16	csum_start;     // 校验开始
			__u16	csum_offset;    // 校验偏移
		};
	};
	__u32			priority;       // 数据包优先级
	kmemcheck_bitfield_begin(flags1);
	__u8			local_df:1,     // 允许切片
				cloned:1,
				ip_summed:2,
				nohdr:1,
				nfctinfo:3;
	__u8			pkt_type:3,
				fclone:2,           // sk_buff 克隆状态
				ipvs_property:1,
				peeked:1,
				nf_trace:1;         // netfilter数据包跟踪标志
	kmemcheck_bitfield_end(flags1);
	__be16			protocol;       // 来自驱动程序的数据包协议

	void			(*destructor)(struct sk_buff *skb);
#if defined(CONFIG_NF_CONNTRACK) || defined(CONFIG_NF_CONNTRACK_MODULE)
	struct nf_conntrack	*nfct;
#endif
#ifdef NET_SKBUFF_NF_DEFRAG_NEEDED
	struct sk_buff		*nfct_reasm;
#endif
#ifdef CONFIG_BRIDGE_NETFILTER
	struct nf_bridge_info	*nf_bridge; // 有关桥接帧的已保存数据-请参见br_netfilter.c
#endif

	int			skb_iif;        // 到达的设备的ifindex

	__u32			rxhash;     // 接收时计算的数据包哈希

	__u16			vlan_tci;   // VLAN标签控制信息

#ifdef CONFIG_NET_SCHED
	__u16			tc_index;	/* traffic control index */
#ifdef CONFIG_NET_CLS_ACT
	__u16			tc_verd;	/* traffic control verdict */
#endif
#endif

	__u16			queue_mapping;
	kmemcheck_bitfield_begin(flags2);
#ifdef CONFIG_IPV6_NDISC_NODETYPE
	__u8			ndisc_nodetype:2;
#endif
	__u8			ooo_okay:1;
	__u8			l4_rxhash:1;
	__u8			wifi_acked_valid:1;
	__u8			wifi_acked:1;
	__u8			no_fcs:1;
	/* 9/11 bit hole (depending on ndisc_nodetype presence) */
	kmemcheck_bitfield_end(flags2);

#ifdef CONFIG_NET_DMA
	dma_cookie_t		dma_cookie;
#endif
#ifdef CONFIG_NETWORK_SECMARK
	__u32			secmark;
#endif
	union {
		__u32		mark;
		__u32		dropcount;
		__u32		avail_size;
	};

	sk_buff_data_t		transport_header;     // 传输头
	sk_buff_data_t		network_header;       // 网络头
	sk_buff_data_t		mac_header;           // 链路层头部
	/* These elements must be at the end, see alloc_skb() for details.  */
	sk_buff_data_t		tail;
	sk_buff_data_t		end;
	unsigned char		*head,
				*data;
	unsigned int		truesize;       // buffer 大小
	atomic_t		users;              // skb被克隆引用的次数，在内存申请和克隆时会用到
};

 ```

 > The kernel maintains all sk_buff structures in a doubly link list.