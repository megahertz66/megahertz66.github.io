---
title: LINUX驱动(1)--LED 驱动
date: 2021-01-25
categories:
- Linux
tags:
- Driver
---

之前上大学时为了看而看的看了一遍韦东山老师的视频。结果两年的时间已经忘得七七八八，现在打算重新开始，认认真真的学起来。反正这里也没人看，那么正是我记录我自己学习成长的好地方……  
尽量练习使用英语，只有大量的使用才会提高。

## 准备工作

所有的网络搭建已经记录在之前的[文章](https://megahertz66.github.io/software-configuration/2021/01/08/%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%BD%91%E7%BB%9C%E8%AE%BE%E7%BD%AE/)中了，这里再记录一下命令方便以后直接使用。

`ifconfig eth0 192.168.11.11;ip route add default dev eth0 via 192.168.11.123;mount -t nfs -o intr,nolock,rsize=1024,wsize=1024 192.168.1.66:/home/zh/sc2440/nfs_mount /mnt`

set ip address; set the gateway; mount nfs file system

## 基本命令

**Install the module**
`insmod xxx.ko` 

**Check current drive**
`lsmod`

**Uninstall the module**
`rmmod xxx`

**Use manual device number setting**
`mknod /dev/xxx c 111 0`


## Use automatic device number setting

If you want to aotomatically create a device number. There are two functions you will need "class_create" and "class_device_creat".You can refer to the following code.
```c
leddrv_class	 = class_create(THIS_MODULE, "first_led");
leddrv_class_dev = class_device_create(leddrv_class, NULL, MKDEV(major, 0), NULL, "leds"); 
```
It will create a file named "first_led" in "/sys/class/". "leds" also will create in "/sys/class/first_led".
The application is called "mdev" and a file named led will be created under "/dev".

## Some functions

1. Register char device
```c

/* In linux-3.4.11/include/linux/fs.h */

static inline int register_chrdev(unsigned int major, const char *name,
				  const struct file_operations *fops)
{
	return __register_chrdev(major, 0, 256, name, fops);
}

static inline void unregister_chrdev(unsigned int major, const char *name)
{
	__unregister_chrdev(major, 0, 256, name);
}

```


static struct char_device_struct {
	struct char_device_struct *next;
	unsigned int major;
	unsigned int baseminor;
	int minorct;
	char name[64];
	struct cdev *cdev;		/* will die */
} *chrdevs[CHRDEV_MAJOR_HASH_SIZE];

struct cdev {
	struct kobject kobj;
	struct module *owner;
	const struct file_operations *ops;
	struct list_head list;
	dev_t dev;
	unsigned int count;
};


The function named "__register_chrdev" have three parts.   
1. "__register_chrdev_region(unsigned int major, unsigned int baseminor,
			   int minorct, const char *name)" 
> "Register a single major with a specified minor range."  

If the first parameter is 0. The function will dynamically alloc the major of the device.
NULL items will be found in the array, and set the major with index of this array.

```c
if (major == 0) {
		for (i = ARRAY_SIZE(chrdevs)-1; i > 0; i--) {
			if (chrdevs[i] == NULL)
				break;
		}

		if (i == 0) {
			ret = -EBUSY;
			goto out;
		}
		major = i;
		ret = major;
	}
```


2. "cdev_alloc"

3. "cdev_add"





## Code

```c

#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/fs.h>
#include <linux/init.h>
#include <linux/delay.h>
#include <asm/uaccess.h>
#include <asm/irq.h>
#include <asm/io.h>
#include <asm/arch/regs-gpio.h>
#include <asm/hardware.h>

/* The LED devices is connected to GPF4 and GPF5 */
#define OUTPUT_5	(1 << 10)
#define OUTPUT_4	(1 << 8) 
#define GPFCON  0x56000050
#define GPFDAT 	0x56000054

volatile unsigned long *gpiof_conf = NULL;
volatile unsigned long *gpiof_dat  = NULL;
int major;

static struct class *leddrv_class;
static struct class_device	*leddrv_class_dev;

static int led_drv_open(struct inode *inode, struct file *file)
{
	*gpiof_conf &= ~( (0x3 << (4*2)) | (0x3 << (5*2)) );
	*gpiof_conf |= ( (0x01 << (4*2))) | (0x01 << (5*2)) );

	printk("led_drv_open\n");
	return 0;
}

static ssize_t led_drv_read(struct file *file, char __user *buf, size_t size, loff_t *ppos)
{
	printk("led_drv_read\n");
	return 0;
}

static ssize_t led_drv_write(struct file *file, char __user *buf, size_t size, loff_t *ppos)
{
	int val; 

	copy_from_user(&val, buf, size);  // copy_to_user
	
	if(val == 1){
		*gpiof_dat |= (1 << 4);
		*gpiof_dat |= (1 << 5);
	}else{
		*gpiof_dat &= ~(1 << 4);
		*gpiof_dat &= ~(1 << 5);
	}
	
	printk("led_drv_write\n");
	return 0;
}

static struct file_operations first_led_fops = {

	.owner = THIS_MODULE,
	.open = led_drv_open,
	.read = led_drv_read,
	.write = led_drv_write,
};



static int led_drv_init(void)
{
	major = register_chrdev(0, "first_led", &first_led_fops);

	/* Use MDEV mechianism to autonatically create major number	 */
	leddrv_class	 = class_create(THIS_MODULE, "first_led");
	leddrv_class_dev = class_device_create(leddrv_class, NULL, MKDEV(major, 0), NULL, "leds"); // The "leds" is "xxx" in "/dev/xxx" 

	gpiof_conf = (volatile unsigned long *)ioremap(GPFCON, 16);
	gpiof_dat = gpiof_conf + 1;

	return 0;
}

static void len_drv_exit(void)
{
	iounmap(gpiof_conf);

	class_device_unregister(leddrv_class_dev);
	class_destroy(leddrv_class);
	unregister_chrdev(major, "first_led");
	return ;
}



module_init(led_drv_init);

module_exit(len_drv_exit);

MODULE_LICENSE("GPL");

```
