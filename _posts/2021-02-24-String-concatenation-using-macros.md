---
title: String Concatenation Using Macros
date: 2021-02-24
categories:
- Technical-records
tags:
- C
---



```c
#include <stdio.h>

/* 直接拼接 */
#define ZZZ "123"

/* 拼接变量 */
#define XXX(name) ABC##name

/* 字符串化变量 */
#define CCC(name)\
do{             \
    printf("%s\n", "AAAA" #name "CCCC");\
}while(0);


int main()
{
    
    printf("%s\n", "abc" ZZZ "fg");

    int ABCd = 10;
    XXX(d) = 20;
    printf("equ = %d\n", ABCd);

    CCC(BBBB);
    return 0;
}

```

**OUTPUT**

abc123fg
equ = 20
AAAABBBBCCCC