

What will happen if you execute the "echo 1> / proc / sys / net / ipv6 / conf / br0 / disable_ipv6" command.



In  "/home/hert/studycode/linux-3.4.11/net/ipv6/addrconf.c" file, There is a very large structure named "addrconf_sysctl_table".

Every option in "/proc/sys/net/inv6/conf/[interface]/" are all in there.



```c
static struct addrconf_sysctl_table
{
	struct ctl_table_header *sysctl_header;
	ctl_table addrconf_vars[DEVCONF_MAX+1];
	char *dev_name;
} addrconf_sysctl __read_mostly = {
	.sysctl_header = NULL,
	.addrconf_vars = {
		{
            ...
            {
			    .procname	= "disable_ipv6",
			    .data		= &ipv6_devconf.disable_ipv6,
			    .maxlen		= sizeof(int),
		    	.mode		= 0644,
		    	.proc_handler	= addrconf_sysctl_disable,
		    },
            ...
        },
};
```

We will see what happens when I execute the "echo 1> / proc / sys / net / ipv6 / conf / br0 / disable_ipv6" command.

First the `proc_handler` will be executed.



```c
int addrconf_sysctl_disable(ctl_table *ctl, int write,
			    void __user *buffer, size_t *lenp, loff_t *ppos)
{
	int *valp = ctl->data;
	int val = *valp;
	loff_t pos = *ppos;
	ctl_table lctl;
	int ret;

	/*
	 * ctl->data points to idev->cnf.disable_ipv6, we should
	 * not modify it until we get the rtnl lock.
	 */
	lctl = *ctl;
	lctl.data = &val;

	ret = proc_dointvec(&lctl, write, buffer, lenp, ppos);

	if (write)
		ret = addrconf_disable_ipv6(ctl, valp, val);
	if (ret)
		*ppos = pos;
	return ret;
}
```

addrconf_disable_ipv6 -> dev_disable_change -> addrconf_notify(NULL, NETDEV_UP, idev->dev)  parameter NETDEV_UP/NETDEV_DOWN.


