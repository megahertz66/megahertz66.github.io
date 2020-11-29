---
title: slabinfo中的size-64和size-128都是什么
date: 2020-05-01
categories:
- Linux
tags:
- Linux
- Linux-kernel
---


在slabinfo中，第一列名字都是通过`kmem_cache_create`的第一个参数 name 生成的。但是里面有很多
像什么`size-64 size-128 size-xxx`这些都是什么玩意，以及在哪里产生的呢？  

## 先说结论  

这些东西是给 kamlloc 使用的。kemalloc 会选择块合适大小的地方来进行内存的申请以便加快内存申请的速度。详细可以看一篇博客[图解slub](http://www.wowotech.net/memory_management/426.html)。
接下来我也会分析这些。  

## size-XXX 是怎么产生的  

就是通过 `mm\slab.c` 中下面这种函数生成的：  
```c
sizes[INDEX_AC].cs_cachep = kmem_cache_create(names[INDEX_AC].name,
					sizes[INDEX_AC].cs_size,
					ARCH_KMALLOC_MINALIGN,
					ARCH_KMALLOC_FLAGS|SLAB_PANIC,
					NULL);
```
可是names又是如何进行初始化的呢？names是这么定义的：
```c
static struct cache_names __initdata cache_names[] = {
#define CACHE(x) { .name = "size-" #x, .name_dma = "size-" #x "(DMA)" },
#include <linux/kmalloc_sizes.h>
	{NULL,}
#undef CACHE
};
```

**一下就头大了，有下面的几个问题**：
1. 谁调用了这个CACHE这个宏？  答：include\linux\slab_def.h 文件中。
2. 为啥用完了马上 #undef ？
3. 这个中间的 #include 又是什么鬼？  

直接就蒙了，后来才明白(其实该开始以为的根本就不对)。  

之前以为 ~~这种初始化在声明的时候才会调用到里面的`#include`中的头文件，而头文件中就是一大堆关于`CHANE`的声明。~~   
 **其实根本不是**，经过实验（下面有实验代码）。这种声明方式只是在头文件的展开位置是位于`#define & #undef` 之间。如果这个头文件中包含了别的宏，那么是否可以使用要看这个头文件在文件中定义的物理位置（就是#define XXX 之前还是之后），如果是之前的话就可以使用，如果是之后的话就不可以使用。**哪怕是这个宏被写在了某一个函数之中，那么调用过这个函数之后仍不能认为是在调用函数位置定义头文件！**   

 内核这么做就是想比较容易的定义出一个静态的数组。通过头文件的形式还很容易修改和管理（我认为，要是还有别的用意欢迎留言！）


## 实验代码
```c
/*
	main 文件
*/
#include <stdio.h>

void test_function(void);


int main()
{
    //printf("in main func %d\n", PPP);  此时调用会报错
    test_function();
    //printf("in main func %d\n", PPP); // 此时调用会报错

#define CHACHE(X)  {printf("in main %d\n", X);}  
#include "print.h"
int c = 10;
printf(" print C = %d\n", c);
printf("in main func %d\n", PPP);
#undef CHACHE

printf(" print C = %d\n", c);
    return 0;
}

void test_function(){

#define CHACHE(X) {printf("in func %d\n", X);}    
#include "print.h"
#undef CHACHE

}


/*
	头文件
*/
#define PPP     1234
CHACHE(123456)
CHACHE(456723)
CHACHE(345645)
CHACHE(12123)
CHACHE(90)

/*
	输出
*/
in func 123456
in func 456723
in func 345645
in func 12123
in func 90
in main 123456
in main 456723
in main 345645
in main 12123
in main 90
 print C = 10
in main func 1234
 print C = 10
```

