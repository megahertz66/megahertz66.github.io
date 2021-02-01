---
title: Analyze Busybox--ifconfig
date: 2021-01-25
categories:
- Linux
tags:
- busybox
---


Ifconfig is a command we often use. Now I will analyze the ifconfig command in busybox.

**Busybox Version: 1.32.1**

If we enter the command "ifconfig" without any parameters, the screen output is as follow.
![ifconfig](/picture/ifconfig-C.png)  

The opints we often focus on IP address and MAC address. But where are they from?

A function named "display_interfaces" will be called if have not any parameters.

```c
if (argv[0] && argv[0][0] == '-' && argv[0][1] == 'a' && !argv[0][2]) {
		++argv;
		show_all_param = IFNAME_SHOW_DOWNED_TOO;
	}

int FAST_FUNC display_interfaces(char *ifname)
{
	struct interface *ife;
	int res;
	struct iface_list ilist;

	ilist.int_list = NULL;
	ilist.int_last = NULL;
	if_readlist(&ilist, ifname != IFNAME_SHOW_DOWNED_TOO ? ifname : NULL);

	if (!ifname || ifname == IFNAME_SHOW_DOWNED_TOO) {
		for (ife = ilist.int_list; ife; ife = ife->next) {

			BUILD_BUG_ON((int)(intptr_t)IFNAME_SHOW_DOWNED_TOO != 1);

			res = do_if_print(ife, (int)(intptr_t)ifname);
			if (res < 0)
				goto ret;
		}
		return 0;
	}

	ife = add_interface(&ilist, ifname);
	res = do_if_print(ife, /*show_downed_too:*/ 1);
 ret:
	return (res < 0); /* status < 0 == 1 -- error */
}
```


