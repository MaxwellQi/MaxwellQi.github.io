---
layout: post
title: Ping 和 traceroute的原理及实现
description: 了解ping和traceroute的原理以及实现简单的ping和tracert
category: blog
tag: net
---

## icmp协议

`ICMP`协议(Internet Control Message Protocol)是互联网协议族的核心协议之一。它用于`TCP/IP`网络中发送控制消息，提供可能发生在通信环境中的各种问题反馈，通过这些信息，使管理者可以对所发生的问题作出诊断，然后才去适当的措施解决。

`ICMP`依靠ip来完成它的任务，它是ip的主要部分。它与传输协议（如TCP和UDP）显著不同：它一般不用于在两点间传输数据。它通常不由网络程序直接使用，除了ping和traceroute这两个特別的例子。

### 技术说明

CMP是在RFC 792中定义的互联网协议族之一。通常用于返回的错误信息或是分析路由。ICMP错误消息总是包括了源数据并返回给发送者。 ICMP错误消息的例子之一是TTL值过期。每个路由器在转发数据报的时候都会把IP包头中的TTL值减1。如果TTL值为0，“TTL在传输中过期”的消息将会回报给源地址。 每个ICMP消息都是直接封裝在一个IP数据包中的，因此，和UDP一样，ICMP是不可靠的。

虽然ICMP是包含在IP数据包中的，但是对ICMP消息通常会特殊处理，会和一般IP数据包的处理不同，而不是作为IP的一个子协议来处理。在很多时候，需要去查看ICMP消息的內容，然后发送过当的错误消息到那个原來产生IP数据包的程序，即那个导致ICMP信息被传送的IP数据包。

很多常用的工具是基于ICMP消息的。traceroute是通过发送包含有特殊的TTL的包，然后接收ICMP超超消息和目标不可达消息來实现的。ping则是用ICMP的"Echo request"（类别代码：8）和"Echo reply"（类别代码：0）消息來实现的。

### ICMP报文结构

#### 报头

ICMP报头从IP报头的第160位开始(ip首部20字节)

![](https://ws2.sinaimg.cn/large/006tNbRwly1fuhcj6vgb4j30a602t74i.jpg)

* Type: ICMP的类型，标识生成的错误报文
* Code: 进一步割分ICMP的类型，该字段用来查找产生错误的原因；例如ICMP的目标不可达类型可以把这个位设置为1-15等来表示不同的意思。
* Checksum : 校验码部分，这个字段包含有从ICMP报头和数据部分计算得来，用于检查错误的数据，其中此校验码字段的值视为0
* ID ：这个字段包含了ID值，在`Echo Reply`类型的消息中要返回这个字段
* Sequence : 这个字段包含一个序号，同样要在`Echo Reply`类型的消息中要返回这个字段

#### 填充数据

填充的数据紧接在ICMP报头的后面(以8位为一组)：

* Linux的ping工具填充的ICMP除了8个8位元组的报头以外，默认情况下还另外填充数据使得总大小位64字节
* Windows的ping.exe填充的ICMP除了8个8位元组的报头以外，默认情况下还另外填充数据使得总大小位40字节 

## Ping

### ping的实现原理

`Ping`是为了测试另一台主机是否可达，现在已经成为一种常用的网络状态检查工具。该程序发送一份 ICMP回显请求报文给远程主机，并等待返回 ICMP回显应答。

ping 使用的是ICMP协议，它发送icmp回送请求消息给目的主机。ICMP协议规定：目的主机必须返回ICMP回送应答消息给源主机。如果源主机在一定时间内收到应答，则认为主机可达。大多数的 TCP/IP 实现都在内核中直接支持Ping服务器，ICMP回显请求和回显应答报文如下图所示。

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fuhdk9xz9yj30dk04cmxk.jpg)

ping的原理是用类型码为8的ICMP发请求，受到请求的主机则用类型码为0的ICMP回应。通过计算ICMP应答报文数量和与接受与发送报文之间的时间差，判断当前的网络状态。这个往返时间的计算方法是：ping命令在发送ICMP报文时将当前的时间值存储在ICMP报文中发出，当应答报文返回时，使用当前时间值减去存放在ICMP报文数据中存放发送请求的时间值来计算往返时间。ping返回接受到的数据报文字节大小、TTL值以及往返时间。

### 利用wireshark查看ping

我在命令行中ping www.baidu.com 以下是显示结果：

![](https://ws4.sinaimg.cn/large/0069RVTdgy1fuo86hffz0j30wg0kngs8.jpg)

 
![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuo88gdu0gj30vv0m4gsd.jpg)

如上图所示，icmp包的type是8 ， 是request请求； icmp的包type是0 ，是reply. 

## traceroute

### traceroute原理

程序是利用增加存活时间(TTL)值来实现功能的。每当一个icmp包经过一个路由器时，其存活时间值就会减1，当其存活时间为0时，路由器便会取消包发送，并发送一个ICMP TTL封包给原封包发出者。 

### traceroute实现原理

主叫方首先发出TTL = 1 的数据包，第一个路由器将 TTL 减1得0后就不再继续转发此数据包，而是返回一个ICMP超时报文，主叫方从超时报文中即可提取出数据包所经过的第一个路由器的地址。然后又发出一个TTL=2的ICMP数据包，可获得第二个路由器的地址，依次增加TTL便获取了沿途所有路由器位地址。

需要注意的是，并不是所有路由器都会如实返回ICMP超时报文。出于安全性考虑，大多数防火墙以及启动了防火墙功能的路由器缺省配置为不返回各种ICMP报文，其路由器或交换机也可被管理员主动修改配置变为不返回ICMP报文。因此Traceroute程序不一定能拿全所有沿途路由器地址。所以当某个TTL值的数据包得不到响应是，并不能停止这一追踪过程，程序仍然会把TTL递增而发出下一个数据包。一直达到预设或用于参数制定的追踪限制时才结束追踪。 

依据上述原理，利用了UDP数据包的Traceroute程序在数据包到达真正的目的主机时，就可能因为该主机没有提供UDP服务而简单将数据包丢弃，并不返回任何信息。为了解决这个问题，Traceroute故意使用了一个大于30000的端口号，因UDP协议规定端口号必须小于30000，所以目标主机收到数据包后唯一能做的事就是返回一个"端口不可达"的ICMP报文，于是主叫方就将端口不可达报文当做跟踪结束标志。

### 利用wireshark查看traceroute

我在命令行中traceroute www.baidu.com 以下是显示结果：

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fuo9lpzgi3j30sl0it45e.jpg)

如上图所示，UDP请求，第一个请求的端口是33435 ， 接下来的UDP请求，端口会递增。 


![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuo9nz1a3hj30sn0ko7bt.jpg)

如上图所示，是路由器返回的ICMP包，type是11




