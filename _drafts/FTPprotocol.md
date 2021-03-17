
**RFC595 File transfet protocol**

### FTP Commands

CDUP - Change to Parent Directory
SMNT - Structure Mount
STOU - Store Unique
RMD - Remove Directory
MKD - Make Directory
PWD - Print Directory
SYST - System


### ASCII

In FTP，ASCII characters are defined to be the lower half of an eight-bit code set.

### Access Controls

### Byte Size

The logical byte size of the file and the transfer byte size used for the transmission of the data.
The transfersizee is always 8 bits.

### Control Connection

This connection follows the Telnet Protocol.
The data transferred my be a part of a file, an entire file or a number of files.
**END-of-Line** The sequence is Carriage Return, fllowed bt Line Feed. In C language EOF is '-1' because ASCII [0~255].

The control connection follows the Telnet protocol.
The FTP uses the Telnet protocol on the control connection.  
  

These data transfer commands include
the MODE command which specify how the bits of the data are to be
transmitted, and the STRUcture and TYPE commands, which are used to
define the way in which the data are to be represented

NVT(Network Virtual Terminal)  

**Using the standard NVT-ASCII representation means that data must be interpreted as 8-bit bytes.**  

EBCDIC (Extended Binary Coded Decimal Interchange Code) 为国际商用机器公司(IBM)于1963年-64年间推出的字符编码表，根据早期打孔机式的二进化十进数(BCD, Binary Coded Decimal)排列而成（广义二进制编码的十进制交换码）。在一个EBCDIC的文件里，每个字母或数字字符都被表示为一个8位的二进制数（一个0、1字符串）。256个可能的字符被定义（字母，数字和一些特殊字符）。
IBM的个人计算机和工作站操作系统不使用它们所有的EBCDIC编码。相反的，它们使用文本的工业标准编码，ASCII码。转化程序允许不同的操作系统从一种编码到另一种编码的转化。它的缺点是：英文字母不是连续地排列，中间出现多次断续，为撰写程序的人带来了一些困难。 

The User-PI may specify a no-default user sude data port with the PORT command.
The User-PI may request the server side to identify a non-default server side data port wiith the PASV command.




