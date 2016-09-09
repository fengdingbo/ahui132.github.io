---
layout: page
title:	抓包工具
category: blog
description:
---
# Preface
网络调试，可以用 wireshark，tshark 或 ngrep。

- wireshark 和 tshark：抓包和网络调试
- ngrep：从网络层摘取信息
- tPacketCapture 不翻墙抓包
- nc: netcat 被誉为网络安全界的‘瑞士军刀' ，一个简单而有用的工具，透过使用TCP 或UDP 协议的网络连接去读写数据。
它被设计成一个稳定的后门工具，能够直接由其它程序和脚本轻松驱动。同时，它也是一个功能强大的网络调试和探测工具，
- mitmproxy: Use mitmproxy to read and modify HTTPS traffic

# mitmproxy
https://blog.heckel.xyz/2013/07/01/how-to-use-mitmproxy-to-read-and-modify-https-traffic-of-your-phone/#Attacking-HTTP-connections

# todo

## tcpdump & wireshark
http://www.bo56.com/%E7%BA%BF%E4%B8%8Aphp%E9%97%AE%E9%A2%98%E6%8E%92%E6%9F%A5%E6%80%9D%E8%B7%AF%E4%B8%8E%E5%AE%9E%E8%B7%B5/

Capturing mobile phone traffic on wireshark
http://stackoverflow.com/questions/9555403/capturing-mobile-phone-traffic-on-wireshark

For Android phones, any network: Root your phone, then install tcpdump on it. This app is a tcpdump wrapper that will install tcpdump and enable you to start captures using a GUI. Tip: You will need to make sure you supply the right interface name for the capture and this varies from one device to another, eg -i eth0 or -i tiwlan0 - or use -i any to log all interfaces
For Android 4.0+ phones: Android PCAP from Kismet uses the USB OTG interface to support packet capture without requiring root. I haven't tried this app, and there are some restrictions on the type of devices supported (see their page)
For Android phones: tPacketCapture uses the Android VPN service to intercept packets and capture them. I have used this app successfully, but it also seems to affect the performance with large traffic volumes (eg video streaming)
For IOS 5+ devices, any network: iOS 5 added a remote virtual interface (RVI) facility that lets you use Mac OS X packet trace programs to capture traces from an iOS device. See here for more details
For all phones, wi-fi only: Set up your PC as a wireless access point, then run wireshark on the PC
For all phones, wi-fi only: Get a capture device that can sniff wi-fi. This has the advantage of giving you 802.11x headers as well, but you may miss some of the packets
Capture using a VPN server: Its fairly easy to set-up your own VPN server using OpenVPN. You can then route your traffic through your server by setting up the mobile device as a VPN client and capture the traffic on the server end.


# Cocoa Packet Analyzer
http://www.tastycocoabytes.com/cpa/
做apple 应用时tcp 工具

# wireshark
> http://www.bo56.com/tcpdump-%E5%92%8C-wireshark%E7%BB%84%E5%90%88%E6%8B%B3%EF%BC%8C%E6%8F%AA%E5%87%BA%E6%9C%89%E9%97%AE%E9%A2%98%E7%9A%84%E6%9C%BA%E5%99%A8/

Support: http, tcp, udp
Licence: GPL

> 同类工具有微软的network monitor,sniffer

## install
	brew cask install xquartz
	brew cask install wireshark

## Usage
On Mac:
	http://blog.csdn.net/phunxm/article/details/38590561
On Windows:
	http://www.cnblogs.com/tankxiao/archive/2012/10/10/2711777.html

WireShark 主要分为这几个界面

1. Display Filter(显示过滤器)，  用于过滤
2. Packet List Pane(封包列表)， 显示捕获到的封包， 有源地址和目标地址，端口号。 颜色不同，代表
3. Packet Details Pane(封包详细信息), 显示封包中的字段
4. Dissector Pane(16进制数据)
5. Miscellanous(地址栏，杂项)

## Packet List Pane
封包列表的面板中显示，编号，时间戳，源地址，目标地址，协议，长度，以及封包信息。 你可以看到不同的协议用了不同的颜色显示。
你也可以修改这些显示颜色的规则，  View ->Coloring Rules.

## Packet Details Pane
封包详细信息 各行信息分别为

	Frame:   物理层的数据帧概况
	Ethernet II: 数据链路层以太网帧头部信息
	Internet Protocol Version 4: 互联网层IP包头部信息
	Transmission Control Protocol:  传输层T的数据段头部信息，此处是TCP
	Hypertext Transfer Protocol:  应用层的信息，此处是HTTP协议

## Filter
过滤器有两种，
一种是显示过滤器，就是主界面上那个，用来在捕获的记录中找到所需要的记录
一种是捕获过滤器，用来过滤捕获的封包，以免捕获太多的记录。 在Capture -> Capture Filters 中设置

### Display Filter
位于主界面Filter. Example:

	ip.src==192.168.1.1 or ip.dst==192.168.1.2
	ip.src==10.17.10.138

Shortcuts:

	Ctrl+B	add filter

#### Filter Rule
表达式规则(case sensitive)

1. Protocol Filter, 协议过滤

	tcp，只显示TCP协议。
	http
	udp

2. IP 过滤

	比如 ip.src ==192.168.1.102 显示源地址为192.168.1.102，
	ip.dst==192.168.1.102, 目标地址为192.168.1.102

3. 端口过滤

	tcp.port ==80,  端口为80的
	tcp.port eq 80,  端口为80的
	tcp.srcport == 80,  只显示TCP协议的愿端口为80的。

4. Http模式过滤

	http.request.method=="GET",   只显示HTTP GET方法的。
	http.host=="hilojack.com"
	http.host==hilojack.com

5. 逻辑运算符为 and or

	adn &&
	or ||

Example:

	ip.src ==192.168.1.102 or ip.dst==192.168.1.102	 源地址或者目标地址是192.168.1.102
	http and (ip.src ==192.168.1.102 or ip.dst==192.168.1.102)
	tcp && http.host==hilojack.com

#### Save
Select the filter you no longer want and click Remove.

	Edit > Preferences > Filter Expressions.

### Capture Filter

# charles

# sysdig
strace+tcpdump+lsof

# netcat
http://blog.csdn.net/turkeyzhou/article/details/6228764

# tcpdump
参考[tcpdump](/p/linux-tcpdump)

## todo
《神探tcpdump终结招》-linux命令五分钟系列之四十三
http://roclinux.cn/?cat=3

## command
tcpdump采用命令行方式，它的命令格式为：

	tcpdump [ -adeflnNOpqStvx ] [ -c 数量 ] [ -F 文件名 ]
	　　　　　　　　[ -i 网络接口 ] [ -r 文件名] [ -s snaplen ]
	　　　　　　　　[ -T 类型 ] [ -w 文件名 ] [表达式 ]

	　-a 　　　将网络地址和广播地址转变成名字；
	　-d 　　　将匹配信息包的代码以人们能够理解的汇编格式给出；
	　-dd 　　　将匹配信息包的代码以c语言程序段的格式给出；
	　-ddd 　　　将匹配信息包的代码以十进制的形式给出；
	　-e 　　　在输出行打印出数据链路层的头部信息；
	　-f 　　　将外部的Internet地址以数字的形式打印出来；
	　-l 　　　使标准输出变为缓冲行形式；
	　-n 　　　不把网络地址转换成名字；
	　-t 　　　在输出的每一行不打印时间戳；
	　-v 　　　输出一个稍微详细的信息，例如在ip包中可以包括ttl和服务类型的信息；
	　-vv 　　　输出详细的报文信息；
	　-c 　　　在收到指定的包的数目后，tcpdump就会停止；
	　-F 　　　从指定的文件中读取表达式,忽略其它的表达式；
	　-i 　　　指定监听的网络接口；
	　-r 　　　从指定的文件中读取包(这些包一般通过-w选项产生)；
		-s	Snarf snaplen bytes of data from each packet rather than the default of 65535 bytes.
	　-w 　　　直接将包写入文件中，并不分析和打印出来；
	　-T 　　　将监听到的包直接解释为指定的类型的报文，常见的类型有rpc （远程过程调用）和snmp（简单网络管理协议；）

## example on mac osx
https://support.apple.com/zh-cn/HT202013

	sudo tcpdump -i en0 -s 0 -B 524288 -w ~/DumpFile01.pcap
	# 如果使用的是 VPN 接口，则输入 -i ppp0

Ctrl-C 后，打开数据包追踪文件:

	tcpdump -s 0 -n -e -x -vvv -r ~/DumpFile01.pcap

In the first window, use tcpdump to capture transmission data from/to your NIC (eth0) to a file:

	sudo tcpdump -s 9999 -i eth0 -w myfile.txt

When that's all done, parse the file with strings and grep:

	strings myfile.txt | grep -C 3 "200 OK"

## tcpdump的表达式介绍
　　　表达式是一个正则表达式，tcpdump利用它作为过滤报文的条件，如果一个报文满足表
达式的条件，则这个报文将会被捕获。如果没有给出任何条件，则网络上所有的信息包将会
被截获。
　　　在表达式中一般如下几种类型的关键字，一种是关于类型的关键字，主要包括host，
net，port, 例如 host 210.27.48.2，指明 210.27.48.2是一台主机，net 202.0.0.0 指明
202.0.0.0是一个网络地址，port 23 指明端口号是23。如果没有指定类型，缺省的类型是
host.
　　　第二种是确定传输方向的关键字，主要包括src , dst ,dst or src, dst and src ,
这些关键字指明了传输的方向。举例说明，src 210.27.48.2 ,指明ip包中源地址是210.27.
48.2 , dst net 202.0.0.0 指明目的网络地址是202.0.0.0 。如果没有指明方向关键字，则
缺省是src or dst关键字。
　　　第三种是协议的关键字，主要包括fddi,ip ,arp,rarp,tcp,udp等类型。Fddi指明是在
FDDI(分布式光纤数据接口网络)上的特定的网络协议，实际上它是"ether"的别名，fddi和e
ther具有类似的源地址和目的地址，所以可以将fddi协议包当作ether的包进行处理和分析。
其他的几个关键字就是指明了监听的包的协议内容。如果没有指定任何协议，则tcpdump将会
监听所有协议的信息包。
　　　除了这三种类型的关键字之外，其他重要的关键字如下：gateway, broadcast,less,
greater,还有三种逻辑运算，取非运算是 'not ' '! ', 与运算是'and','&&';或运算 是'o
r' ,'||'；
　　　这些关键字可以组合起来构成强大的组合条件来满足人们的需要，下面举几个例子来
说明。
　　　(1)想要截获所有210.27.48.1 的主机收到的和发出的所有的数据包：
　　　　#tcpdump host 210.27.48.1
　　　(2) 想要截获主机210.27.48.1 和主机210.27.48.2 或210.27.48.3的通信，使用命令：在命令行中适用括号时
　　　　#tcpdump host 210.27.48.1 and / (210.27.48.2 or 210.27.48.3 /)
　　　(3) 如果想要获取主机210.27.48.1除了和主机210.27.48.2之外所有主机通信的ip包:
　　　　#tcpdump ip host 210.27.48.1 and ! 210.27.48.2
　　　(4)如果想要获取主机210.27.48.1接收或发出的telnet包，使用如下命令：
　　　　#tcpdump tcp port 23 host 210.27.48.1

## tcpdump 的输出结果
介绍几种典型的tcpdump命令的输出信息

　　　(1) 数据链路层头信息
　　　使用命令#tcpdump --e host ice
	　　　ice 是一台装有linux的主机，她的MAC地址是0：90：27：58：AF：1A
	　　　H219是一台装有SOLARIC的SUN工作站，它的MAC地址是8：0：20：79：5B：46；
	上一条命令的输出结果如下所示：
		21:50:12.847509 eth0 < 8:0:20:79:5b:46 0:90:27:58:af:1a ip 60: h219.33357 > ice.
		telnet 0:0(0) ack 22535 win 8760 (DF)

　　分析：21：50：12是显示的时间， 847509是ID号，eth0 <表示从网络接口eth0 接受该
数据包，eth0 >表示从网络接口设备发送数据包, 8:0:20:79:5b:46是主机H219的MAC地址,它
表明是从源地址H219发来的数据包. 0:90:27:58:af:1a是主机ICE的MAC地址,表示该数据包的
目的地址是ICE . ip 是表明该数据包是IP数据包,60 是数据包的长度, h219.33357 > ice.
telnet 表明该数据包是从主机H219的33357端口发往主机ICE的TELNET(23)端口. ack 22535
表明对序列号是222535的包进行响应. win 8760表明发送窗口的大小是8760.

　　(2) ARP包的TCPDUMP输出信息
　　　使用命令#tcpdump arp
　　　得到的输出结果是：
	　　22:32:42.802509 eth0 > arp who-has route tell ice (0:90:27:58:af:1a)
	　　22:32:42.802902 eth0 < arp reply route is-at 0:90:27:12:10:66 (0:90:27:58:af:1a)

　　分析: 22:32:42是时间戳, 802509是ID号, eth0 >表明从主机发出该数据包, arp表明是
ARP请求包, who-has route tell ice表明是主机ICE请求主机ROUTE的MAC地址。 0:90:27:5
8:af:1a是主机ICE的MAC地址。

　　(3) TCP包的输出信息
　　　用TCPDUMP捕获的TCP包的一般输出信息是：
　　src > dst: flags data-seqno ack window urgent options
　　src > dst:表明从源地址到目的地址, flags是TCP包中的标志信息,S 是SYN标志, F (F
IN), P (PUSH) , R (RST) "." (没有标记); data-seqno是数据包中的数据的顺序号, ack是
下次期望的顺序号, window是接收缓存的窗口大小, urgent表明数据包中是否有紧急指针.
Options是选项.

　　(4) UDP包的输出信息
　　　用TCPDUMP捕获的UDP包的一般输出信息是：
　　route.port1 > ice.port2: udp lenth
　　UDP十分简单，上面的输出行表明从主机ROUTE的port1端口发出的一个UDP数据包到主机
ICE的port2端口，类型是UDP， 包的长度是lenth

## with wireshark
要让wireshark能分析tcpdump的包，关键的地方是 -s 参数， 还有要保存为 -w文件， 例如下面的例子：

	./tcpdump   -i  eth0  -s  0  -w  SuccessC2Server.pcap   host  192.168.1.20   抓主机上的所有包，让wireshark过滤

	./tcpdump   -i   eth0  'dst host 239.33.24.212'  -w   raw.pcap    抓包的时候就进行过滤

wireshark 打开这个抓包数据文件并过滤

	tcp.port   eq   5541
	ip.addr   eq   192.168.2.1

过滤出来后， 用 fllow tcp 查看包的内容
