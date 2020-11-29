# 网络编程下的ioctl   
  
  
----------------  
  
  
ioctl 在man手册的解释是说ioctl() system call mainpulates underlying device parameters of special file.
就是可以操作底层驱动设备的参数。所以说是作为用户空间和内核空间交互的方式之一，在设备驱动中也是经常使用。
ioctl也是有用户空间和内核空间两个不同的版本。  
  
  
## 函数原型  

```
 #include <sys/ioctl.h>

 int ioctl(int fd, unsigned long request, ...);
```

第三个参数经常是一个指针，其类型也会根据request的类型的变化而变化。
这里主要讨论用户空间的ioctl的使用。
  
    
## 常用结构  

```
   struct ifconf
  {
    int	ifc_len;			/* Size of buffer.  */
    union
      {
	__caddr_t ifcu_buf;
	struct ifreq *ifcu_req;
      } ifc_ifcu;
  };
  
  # define ifc_buf	ifc_ifcu.ifcu_buf	/* Buffer address.  */
  # define ifc_req	ifc_ifcu.ifcu_req	/* Array of structures.  */
  
  /* Length of interface name.  */
  #define IF_NAMESIZE	16
  
  struct ifreq
  {
    # define IFHWADDRLEN	6
    # define IFNAMSIZ	IF_NAMESIZE
    union
      {
	char ifrn_name[IFNAMSIZ];	/* Interface name, e.g. "en0".  */
      } ifr_ifrn;

    union
      {
	struct sockaddr ifru_addr;
	struct sockaddr ifru_dstaddr;
	struct sockaddr ifru_broadaddr;
	struct sockaddr ifru_netmask;
	struct sockaddr ifru_hwaddr;
	short int ifru_flags;
	int ifru_ivalue;
	int ifru_mtu;
	struct ifmap ifru_map;
	char ifru_slave[IFNAMSIZ];	/* Just fits the size */
	char ifru_newname[IFNAMSIZ];
	__caddr_t ifru_data;
      } ifr_ifru;
  };
  
# define ifr_name	ifr_ifrn.ifrn_name	/* interface name 	*/
# define ifr_hwaddr	ifr_ifru.ifru_hwaddr	/* MAC address 		*/
# define ifr_addr	ifr_ifru.ifru_addr	/* address		*/
# define ifr_dstaddr	ifr_ifru.ifru_dstaddr	/* other end of p-p lnk	*/
# define ifr_broadaddr	ifr_ifru.ifru_broadaddr	/* broadcast address	*/
# define ifr_netmask	ifr_ifru.ifru_netmask	/* interface net mask	*/
# define ifr_flags	ifr_ifru.ifru_flags	/* flags		*/
# define ifr_metric	ifr_ifru.ifru_ivalue	/* metric		*/
# define ifr_mtu	ifr_ifru.ifru_mtu	/* mtu			*/
# define ifr_map	ifr_ifru.ifru_map	/* device map		*/
# define ifr_slave	ifr_ifru.ifru_slave	/* slave device		*/
# define ifr_data	ifr_ifru.ifru_data	/* for use by interface	*/
# define ifr_ifindex	ifr_ifru.ifru_ivalue    /* interface index      */
# define ifr_bandwidth	ifr_ifru.ifru_ivalue	/* link bandwidth	*/
# define ifr_qlen	ifr_ifru.ifru_ivalue	/* queue length		*/
# define ifr_newname	ifr_ifru.ifru_newname	/* New name		*/
```
其他更详细的可以看linux kernel code net\if.h 文件
  
  
## 设置网桥   

代码出自 brctl 源码。  

`ioctl(fd, SIOCBADDBR, brname)  //添加网桥 此处brname是一个char*`  
`ioctl(fd, SIOCBADELBR, brname) //删除网桥`
```
/* 向网桥中添加设备 */
struct ifreq ifr;
int ifIndex = if_nametoindex(dev);
strncpy(ifr.ifr_name, brige_name, IF_NAMESIZE);
ifr.ifr_ifindex = ifIndex;
ioctl(fd, SIOCBRADDIF, &ifr);
```
  
## 设置ip  

在dhcp6c的源码中通过以下操作设置PA Ip 

```
	if ((s = socket(PF_INET6, SOCK_DGRAM, IPPROTO_UDP)) < 0) {
		dprintf(LOG_ERR, FNAME, "can't open a temporary socket: %s",
		    strerror(errno));
		return (-1);
	}
	
	memset(&ifr, 0, sizeof(ifr));
	strncpy(ifr.ifr_name, ifname, IFNAMSIZ - 1);
	if (ioctl(s, SIOGIFINDEX, &ifr) < 0) {
		dprintf(LOG_NOTICE, FNAME, "failed to get the index of %s: %s",
		    ifname, strerror(errno));
		close(s); 
		return (-1); 
	}
	memcpy(&req.ifr6_addr, &addr->sin6_addr, sizeof(struct in6_addr));
	req.ifr6_prefixlen = plen;
	req.ifr6_ifindex = ifr.ifr_ifindex;
	
	if (ioctl(s, ioctl_cmd, &req)) {
		dprintf(LOG_NOTICE, FNAME, "failed to %s an address on %s: %s",
		    cmdstr, ifname, strerror(errno));
		close(s);
		return (-1);
	}
```  

*其他的也可以看以下 UNIX网络编程的第17章*