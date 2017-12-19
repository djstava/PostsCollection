### 软硬件环境
* ubuntu kylin

### 概述  
DHCP是一个局域网的网络协议，主要用途是给内部网络或网络服务供应商自动分配IP地址给用户.给内部网络管理员作为对所有计算机作中央管理的手段,它是一种C/S的结构，请见下图。

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_01.png)

### 协议结构  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_02.png)

Op – 消息操作代码，既可以是引导请求（BOOTREQUEST）也可以是引导答复（BOOTREPLY）  
Htype – 硬件地址类型  
Hlen – 硬件地址长度  
Xid –处理ID  
Secs –从获取到IP地址或者续约过程开始到现在所消耗的时间  
Flags –标记  
Ciaddr –客户机 IP地址  
Yiaddr –“你的”（客户机）IP 地    址
Siaddr –在 bootstrap 中使用的下一台服务器的IP地址  
Giaddr –用于导入的接替代理IP地址  
Chaddr –客户机硬件  
Sname –任意服务器主机名称，空终止符  
File –DHCP 发现协议中的引导文件名、空终止符、属名或者空，DHCP供应协议中的受限目录路径名  
Options –可选参数字  

### DHCP工作流程  

首先来熟悉下DHCP交互过程中的几个重要消息：

消息名称           |           详细说明
:----------------|:-----------------------------------------------
DHCPDISCOVER    |  客户端发起广播消息去寻找DHCP服务器
DHCPOFFER       |  空闲的DHCP服务器响应DHCPDISCOVER请求，附带了配置信息(如可用的IP地址，子网掩码等)
DHCPREQUEST     |   再次广播消息，申请DHCPOFFER中返回的IP地址
DHCPACK         |   DHCP服务器将IP分配给客户端
DHCPNAK         |   DHCP服务器返回的消息告知客户端申请的网络地址信息有误
DHCPDECLINE     |   DHCP服务器返回的消息告知客户端申请的网络地址已被使用
DHCPRELEASE     |   客户端释放获取到的IP，取消余下的租约时间
DHCPINFORM      |   客户端通过其它手段已经获取到IP地址，请求获取其它配置信息  


了解了这几个消息后，再来看看DHCP的交互过程，应该会更容易理解一点。先来看一张图，是通过抓包工具wireshark捕获到的申请IP成功的信息  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_03.png)

1、client以广播包的形式，寻找可以分配ip地址的DHCP server，这即为discover阶段

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_04.png)

2、所有收到这个discover广播包的DHCP server，会回应这个广播包；发包的方式可能是广播报文，也可能是单播报文 ，此为offer阶段  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_05.png)

从上图中可以看到，DHCP服务器的IP地址是10.27.70.254，说明10.27.70.254最先响应了discover广播，并告知了DNS、Subnet Mask、IP、Router、Lease time信息。

3、client收到这个包后(如果网络中有多个DHCP server，那么client只会接受第一个到达的offer报文)，client从offer报文中提取所要的ip，dns，router等， 然后以广播包的形式，告知所有的DHCP server，我已经得到一个IP，并且此报文中包含了为client分配ip地址的server的信息。这即为request阶段。  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_06.png)

如图所示，客户端请求的IP地址是10.27.70.22，这里也是一个广播包。

4、局域网中所有的DHCP server收到request广播包之后，从中提取出服务器的信息，如果是指向自己的，则会返回ACK，其他的服务器则不会返回ACK。这即为ACK阶段。  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_07.png)

ACK返回的消息中包含了租约信息和客户端renew的时间点，这样一次成功的交互就完成了。

5、DHCP server向client出租的IP地址都有一个租界期限，期满后DHCP服务器便会回收分配的IP地址。如果DHCP client要延长其IP租约，须更新其IP租约。DHCP客户端在IP租约期限过一半时，会自动向DHCP服务器发送单播的DHCP Request报文续延租期。这就是renew阶段。  

### DHCP Option  

DHCP Option有很多，详细的可以参见RFC文档[http://www.ietf.org/rfc/rfc2131.txt][1]，这里仅仅列出之前使用过的Option。

#### Option 60  

先看看来自rfc的解释  
This option is used by DHCP clients to optionally identify the vendor type and configuration of a DHCP client.The information is a string of n octets, interpreted by servers.Vendors may choose to define specific vendor class identifiers to convey particular configuration or other identification information about a client. For example, the identifier may encode the client's hardware configuration. Servers not equipped to interpret the class-specific information sent by a client MUST ignore it (although it may be reported). Servers that respond SHOULD only use option 43 to return the vendor-specific information to the client.)  

在实际应用中的某些特殊网络，DHCP server需要client通过option 60来标识自己的设备类型或配置，DHCP server会直接忽略option 60不符的client，这样就能起到给客户端做认证的效果，不满足条件的不能在此网络中使用。

### DHCP Server  

手头上只有ubuntu的desktop版本，就以它为例吧，在ubuntu server版上也是一样。在ubuntu上使用得最多的，就属isc-dhcp-server了，首先在系统中利用apt-get来安装
sudo apt-get install isc-dhcp-server

启动服务

	sudo /etc/init.d/isc-dhcp-server start

#### DHCP配置文件dhcp.conf

配置文件位于/etc/dhcp/下

	ddns-update-style none;
	option domain-name “djstavav”;
	option domain-name-servers 202.96.209.5, 202.96.209.133;

	//默认的租约时间，单位是秒
	default-lease-time 600;
	max-lease-time 720;

	//syslog设置，可以到/var/log/syslog文件查看DHCP分配的日志
	log-facility local7;

	//这部分是重点，分别了可分配IP的范围，子网掩码
	subnet 10.27.81.0 netmask 255.255.255.0 {
	interface eth0;
	range 10.27.81.63 10.27.81.254;
	option subnet-mask 255.255.255.0;
	option broadcast-address 10.27.81.255;
	}

这里有一点需要注意，如果在一个需要持续分配IP的环境下，租约时间建议不要设得太长，否则当新机器上线后，会因为IP池已满而无法获取到IP，这时/var/log/syslog会显示

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/dhcp/dhcp_08.png)

#### 特定MAC地址分配固定IP

修改dhcp.conf，增加如下语句，请根据自己实际情况自行修改MAC和IP地址

	host djstava {   
	 hardware ethernet dc:2a:14:e0:02:26;
	 fixed-address 10.27.70.25;
	}

### DHCP Client & Server代码实现

请参考[http://freecode.com/projects/udhcp][2]

### 参考文献
1、[http://en.wikipedia.org/wiki/Dynamic\_Host\_Configuration\_Protocol\\][3]  
2、[http://www.networksorcery.com/enp/protocol/bootp/options.htm\\][4]  
3、[http://freecode.com/projects/udhcp][5]

[1]:	http://www.ietf.org/rfc/rfc2131.txt
[2]:	http://freecode.com/projects/udhcp
[3]:	http://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol%5C
[4]:	http://www.networksorcery.com/enp/protocol/bootp/options.htm%5C
[5]:	http://freecode.com/projects/udhcp
