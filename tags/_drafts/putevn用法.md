# putevn用法

**结论**： putenv只能更改当前程序中的环境变量，对外部环境变量无影响。可以针对这种特点使得 C 函数调用 shel l函数带来帮助。



## 举栗说明

先有一功能是使用DHCP option 中携带 TFTP server IP ，客户端使用IP对配置文件进行下载。




