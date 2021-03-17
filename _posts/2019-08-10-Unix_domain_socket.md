---
title: Unix domain socket 
date: 2019-08-10
categories:
- Technical-records
tags:
- Linux
---
 
liux中的一种进程间通信方式就是通过网络套接字进行通讯的。  
> UNIX domain sockets are used to communicate with processes running on the same machine. Although Internet domain sockets can be used for this same purpose, UNIX domain sockets are more efficient. UNIX domain sockets only copy data; they have no protocol processing to perform, no network headers to add or remove, no checksums to calculate, no sequence numbers to generate, and no acknowledgements to send.  --------from APUE 17.3  
> 
## 创建套接字
```c
 #include <sys/types.h>         
 #include <sys/socket.h>
 int socket(int domain, int type, int protocol);
```
和正常创建套接字使用的API是相同的，只不过参数不同。  
**第一个参数**使用AF_LOCAL 或者 AF_UNIX，还有的代码里面是PF_LOCAL或者PFUNIX，在man手册中解释是，P开头的是低版本使用的。
**第二个参数**可以选择数据报或者流式数据对应SOCKTET_DGAM和SOCKET_STREAM  
**第三个参数**设置为0  

## 绑定套接字  
`int bind(int sockfd, const struct sockaddr *addr,socklen_t addrlen);`  
在apue指出，在不同种类的操作系统中bind的第二个参数的结构体定义不同。  
在linux 2.4.22的<sys/un.h>中：  
```c
struct sockaddr_un {
sa_family_t sun_family; /* AF_UNIX */
char sun_path[108]; /* pathname */
};
```
而在其他操作类UNIX系统中可能还有别的成员在结构中，这样最后一个长度参数就需要使用<stddef.h>中的offset宏来处理。这是一个很秀的操作。  
`#define offsetof(TYPE, MEMBER) ((int)&((TYPE *)0)->MEMBER)`  
同样在apue中也举例了如何使用bind函数。（apue真乃神书也！）  
```c
#include "apue.h"
#include <sys/socket.h>
#include <sys/un.h>
int
main(void)
{
int fd, size;
struct sockaddr_un un;
un.sun_family = AF_UNIX;
strcpy(un.sun_path, "foo.socket");
if ((fd = socket(AF_UNIX, SOCK_STREAM, 0)) < 0)
err_sys("socket failed");
size = offsetof(struct sockaddr_un, sun_path) + strlen(un.sun_path);
if (bind(fd, (struct sockaddr *)&un, size) < 0)
err_sys("bind failed");
printf("UNIX domain socket bound\n");
exit(0);
}
```
**sockaddr_un**结构中，第二个参数是一个文件路径，在bind的时候会产生一个sock文件。如果这个文件没有退出的话bind调用就会失败，这也就网上的代码在bind前调用一次unlink的原因。  
这也是一篇学习UNIX domain socket非常好的文章。[Unix域套接字（Unix Domain Socket）介绍-roland_sun](https://blog.csdn.net/Roland_Sun/article/details/50266565)。中间介绍了一种抽象文件路径，这样就避免了每次都要删除一下才能使用和和系统中其他路径重名的问题。同时也很详细的介绍了UNIX domain socket。  

## listen  
```c
#include <sys/types.h> 
#include <sys/socket.h>
int listen(int sockfd, int backlog);
sockfd:
socket文件描述符
backlog:
建立链接数和
```
还是apue中的原封不动的例子：  
```c
#include "apue.h"
#include <sys/socket.h>
#include <sys/un.h>
#include <errno.h>
#define QLEN 10
/*
* Create a server endpoint of a connection.
* Returns fd if all OK, <0 on error.
*/
int
serv_listen(const char *name)
{
    int fd, len, err, rval;
    struct sockaddr_un un;
    /* create a UNIX domain stream socket */
    if ((fd = socket(AF_UNIX, SOCK_STREAM, 0)) < 0)
    return(-1);
    unlink(name); /* in case it already exists */
    /* fill in socket address structure */
    memset(&un, 0, sizeof(un));
    un.sun_family = AF_UNIX;
    strcpy(un.sun_path, name);
    len = offsetof(struct sockaddr_un, sun_path) + strlen(name);
    /* bind the name to the descriptor */
    if (bind(fd, (struct sockaddr *)&un, len) < 0) {
    	rval = -2;
    	goto errout;
    }
    if (listen(fd, QLEN) < 0) { /* tell kernel we're a server */
    	rval = -3;
    	goto errout;
    }
    return(fd);
    errout:
    err = errno;
    close(fd);
    errno = err;
    return(rval);
}
```
**注意！！路径是服务端和连接端自己绑定自己的，然后告诉另外一方自己的路径。**  
> We don't let the system choose a default address for us, because the server would be unable to distinguish one client from another.Instead, we bind our own address, a step we usually don't take when developing a client program that uses sockets.  
> 就是说在UNIX domain socket下连接端也bind一下，原因不清楚，记住就好。   

## 连接端例子
```c
#include "apue.h"
#include <sys/socket.h>
#include <sys/un.h>
#include <errno.h>
#define CLI_PATH "/var/tmp/" /* +5 for pid = 14 chars */
#define CLI_PERM S_IRWXU /* rwx for user only */
/*
* Create a client endpoint and connect to a server.
* Returns fd if all OK, <0 on error.
*/
int
cli_conn(const char *name)
{
    int fd, len, err, rval;
    struct sockaddr_un un;
    /* create a UNIX domain stream socket */
    if ((fd = socket(AF_UNIX, SOCK_STREAM, 0)) < 0)
    	return(-1);
    /* fill socket address structure with our address */
    memset(&un, 0, sizeof(un));
    un.sun_family = AF_UNIX;
    sprintf(un.sun_path, "%s%05d", CLI_PATH, getpid());
    len = offsetof(struct sockaddr_un, sun_path) + strlen(un.sun_path);
    unlink(un.sun_path); /* in case it already exists */
    if (bind(fd, (struct sockaddr *)&un, len) < 0) {
    	rval = -2;
    	goto errout;
    }
    if (chmod(un.sun_path, CLI_PERM) < 0) {
        rval = -3;
        goto errout;
    }
    /* fill socket address structure with server's address */
    memset(&un, 0, sizeof(un));
    un.sun_family = AF_UNIX;
    strcpy(un.sun_path, name);
    len = offsetof(struct sockaddr_un, sun_path) + strlen(name);
    if (connect(fd, (struct sockaddr *)&un, len) < 0) {
    	rval = -4;
    	goto errout;
    }
   	return(fd);
   	
    errout:
    	err = errno;
        close(fd);
        errno = err;
        return(rval);
}
```










