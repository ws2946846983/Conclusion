###ARP协议(broadcast)
地址解析协议，即ARP（Address Resolution Protocol），是根据 IP地址 获取 物理地址 的一个 TCP/IP协议 。   
主机 发送信息时将包含目标IP地址的ARP请求广播到网络上的所有主机，并接收返回消息，以此确定目标的物理地址；   
收到返回消息后将该IP地址和物理地址存入本机ARP缓存中并保留一定时间，下次请求时直接查询ARP缓存以节约资源。

###套接字描述符
采用多路复用I/O监听3个套接字的数据时，如果套接字描述符分别是：5,17,19,则
```
select(int maxfd,struct fd_set* rdset,NULL,NULL)
```
中的maxfd应取为:**最大套接字描述符+1**

###tcpdump用法
tcpdump采用命令行方式，它的命令格式为：   
    　　tcpdump [ -adeflnNOpqStvx ] [ -c 数量 ] [ -F 文件名 ]    
　　　　　　　　　　[ -i 网络接口 ] [ -r 文件名] [ -s snaplen ]    
　　　　　　　　　　[ -T 类型 ] [ -w 文件名 ] [表达式 ]      
选项介绍：
      -a 　　　将网络地址和广播地址转变成名字；  
　　　-d 　　　将匹配信息包的代码以人们能够理解的汇编格式给出；  
　　　-dd 　　 将匹配信息包的代码以c语言程序段的格式给出；   
　　　-ddd 　　将匹配信息包的代码以十进制的形式给出；   
　　　-e 　　　在输出行打印出数据链路层的头部信息；  
　　　-f 　　　将外部的Internet地址以数字的形式打印出来；  
　　　-l 　　　使标准输出变为缓冲行形式；   
　　　-n 　　　不把网络地址转换成名字；   
　　　-t 　　　在输出的每一行不打印时间戳；  
　　　-v 　　　输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息；  
　　　-vv 　　 输出详细的报文信息；  
　　　-c 　　　在收到指定的包的数目后，tcpdump就会停止；  
　　　-F 　　　从指定的文件中读取表达式,忽略其它的表达式；  
　　　-i 　　　指定监听的网络接口；  
　　　-r 　　　从指定的文件中读取包(这些包一般通过-w选项产生)；  
　　　-w 　　　直接将包写入文件中，并不分析和打印出来；  
　　　-T 　　　将监听到的包直接解释为指定的类型的报文，常见的类型有rpc （远程过程调用）和snmp（简单网络管理协议；）
###网络相关命令
```
subnet 166.173.197.131 netmask 255.255.255.192{
range 166.173.197.10 166.173.197.107;
default-lease-time 600;
max-lease-time 7200;
}
```
* ubnet 设置一个子网  166.173.197.131/24  
* range   可分配的IP地址范围上  166.173.197.10 ~ 166.173.197.107 
* default-lease-time 默认租约时间
* max-lease-time 最大租约时间 

###TCP/IP协议
TCP/IP网络协议栈分为
* **应用层（Application）**：Talnet,FTP和e-mail等
* **传输层（Transport）**：TCP和UDP
* **网络层（Network）**：IP、ICMP和IGMP
* **链路层（Link）**：设备驱动程序及接口卡
**传输层及其以下的机制由内核提供，应用层由用户进程提供**，应用程序对通讯数据的含义进行解释，而传输层及其以下处理通讯的细节，将数据从一台计算机通过一定的路径发送到另一台计算机。
应用层数据通过协议栈发到网络上时，每层协议都要加上一个数据首部（header），称为封装   
不同的协议层对数据有不同的称为，**在传输层叫做段，在网络层叫做数据包，在链路层叫做帧**    
网络层负责点对点的传输（主机或路由器），而传输层负责端到端的传输（源主机和目的主机）

####ARP和ICMP
协议字段有三种值，分别对应IP,ARP,RARP   
以太网帧中的数据长度规定最小46字节，最大1400字节，**ARP和RARP数据包的长度不够46字节，要在后面补填充位**。
**MTU这个概念指数据帧中有效载荷的最大长度，不包括帧首部的长度。**   
ICMP协议用于传递差错信息、时间、回显、网络信息等控制数据

####TCP段格式
三方握手    
滑动窗口   
TCP协议是面向流的协议（容易粘包）
UDP协议是面向消息的协议，应用程序必须以消息为单位提取数据       
####TCP如何保证可靠性
* 应用数据被分割成TCP认为最适合发送的数据块，称为段传递给IP层。
* 当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段。
* 当TCP收到发自TCP连接另一端的数据段，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒。
* TCP将保持它首部和数据的校验和。这是一个端到端的校验和，目的是检测数据在传输过程中的任何变化。如果收到段的校验和有差错，TCP将丢弃这个报文段并且不确认（导致对方超时重传）
* TCP承载于IP数据报来传输，而IP数据报的到达可能会失序，因此TCP报文段的到达也可能会失序。TCP将对收到的数据段进行重新排序后呈现在接收缓冲区给应用层提取。
* IP数据报会发生重复，TCP的接收端必须丢弃重复的数据。
* TCP还能提供流量控制。TCP连接的每一方都有一定大小的缓冲空间。

####UDP协议不面向连接，也不保证传输的可靠性
* 发送端的UDP协议层只管把应用层传来的数据封装成段交给IP协议层就算完成任务了，如果因为网络故障该段无法发到对方，
UDP协议层也不会给应用层返回任何错误信息。

* 接收端的UDP协议层只管把收到的数据根据端口号交给相应的应用程序就算完成任务了，如果发送端发来多个数据段并且在网络上经过不同的路由，
到达接收端时顺序已经错乱了，UDP协议层也不保证按发送时的顺序交给应用层。

* 通常接收端的UDP协议层将收到的数据放在一个固定大小的缓冲区中等待应用程序来提取和处理，如果应用程序提取和处理的速度很慢，
而发送端发送的速度很快，就会丢失数据段，UDP协议层并不报告这种错误。   
**基于UDP的TFTP协议一般只用于传送小文件（所以才叫trivial的ftp），而基于TCP的FTP协议适用于各种文件的传输**

####粘包
主要原因：接收方不知道消息之间的界限，不知道一次性提取多少字节的数据所造成的。   
解决方案：
* 定长包
* 包尾加\r\n（ftp）（缺点是如果消息本身含有\r\n字符，则依然分不清）
* 包头加上包体长度
* 更复杂的应用层协议

###五种I/O模型
* 阻塞I/O
* 非阻塞I/O：轮询
* I/O复用
* 信号驱动I/O
* 异步I/O：aio_read 函数也会提供一个buf，系统调用进入内核，如果没有数据则立即返回，进程继续执行其他操作，所以叫异步I/O。  
（异步I/O跟信号驱动I/O的不同之处在于，它不用调用recv进行数据的复制，如果将后者比做”拉pull“，则前者可以认为是”push推“，push的效率会高点）    
同步和异步的区别：**是不是要求处理消息者自己来完成将数据从内核缓冲区复制回进程缓冲区的过程**     
消息者阻塞和非阻塞应该是发生在消息的处理的时刻。阻塞其实就是等待，发出通知，等待结果完成。非阻塞属于发出通知，立即返回结果，没有等待过程。    
阻塞式I/O(默认)，非阻塞式I/O(nonblock)，I/O复用(select/poll/epoll)，信号驱动IO都属于**同步I/O**    

  
TCP协议规定，主动关闭连接的一方要处于TIME_WAIT状态，等待**两个MSL（maximum segment lifetime）**的时间后才能回到CLOSED状态，
需要有MSL 时间的主要原因是在这段时间内如果最后一个ack段没有发送给对方，则可以重新发送。


###OSI七层
* 物理层： RJ45 、 CLOCK 、 IEEE802.3 （中继器，集线器，网关） - 
* 数据链路： PPP 、 FR 、 HDLC 、 VLAN 、 MAC （网桥，交换机） - 
* 网络层： IP 、 ICMP 、 ARP 、 RARP 、 OSPF 、 IPX 、 RIP 、 IGRP 、 （路由器） - 
* 传输层： TCP 、 UDP 、 SPX - 
* 会话层： NFS 、 SQL 、 NETBIOS 、 RPC - 
* 表示层： JPEG 、 MPEG 、 ASII - 
* 应用层： FTP 、 DNS 、 Telnet 、 SMTP 、 HTTP 、 WWW 、 NFS