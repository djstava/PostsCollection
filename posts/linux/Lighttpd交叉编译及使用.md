### 软硬件环境

* ubuntu kylin
* lighttpd-1.4.35
* mips toolchain
* broadcom 7581

### 概述

Web服务器的主要功能是提供网上信息浏览服务。目前主流的Web Server有apache、nginx和kangle等，不过上述这些都体能齐全、体积庞大，如果想要在嵌入式设备上使用它们，显然是不现实的。本文介绍一种轻量级的web server，它能够很好地满足你的要求并且表现出色，它就是lighttpd。    

### 交叉编译

#### 下载源码,本文使用的是1.4.35版本    

​	下载地址: <http://www.lighttpd.net/download/>

#### 编译(安装目录可以自行修改/home/djstava/Downloads/lighttpd-1.4.35/install)    

    tar xvf lighttpd-1.4.35.tar.gz
    cd lighttpd-1.4.35   
    ./configure --prefix=/home/djstava/Downloads/lighttpd-1.4.35/install --host=mipsel-linux-gnu --disable-lfs --disable-ipv6 --without-openssl --without-kerberos5 --without-pcre --without-zlib --without-bzip2 --without-lua "CC=mipsel-linux-gcc -EL" "CFLAGS=-EL" "LDFLAGS=-EL"
    make   
    make install  
#### 配置    

    cd /home/djstava/Downloads/lighttpd-1.4.35/install
    mkdir -p cache cgi-bin config log sockets vhosts webpages
    cp -rf lighttpd-1.4.35/doc/config/config.d lighttpd-1.4.35/doc/config/lighttpd.conf lighttpd-1.4.35/doc/config/modules.conf config/  
 修改config/lighttpd.conf

	var.log_root    = "/home/djstava/Downloads/lighttpd-1.4.35/install/log" 
	var.server_root = "/home/djstava/Downloads/lighttpd-1.4.35/install" 
	var.state_dir   = "/home/djstava/Downloads/lighttpd-1.4.35/install"  
	var.home_dir    = "/home/djstava/Downloads/lighttpd-1.4.35/install"  
	var.conf_dir    = "/home/djstava/Downloads/lighttpd-1.4.35/install/config"    
	
	server.document-root = server_root + "/webpages"    
	
	#server.username  = "lighttpd"  
	#server.groupname = "lighttpd"  
#### 启动服务

```
./sbin/lighttpd -f conf/lighttpd.conf
```

#### 测试页面，将下面代码保存到webpages/index.html    

    <html>
    <head>
    <title>lighttpd test</title>
    </head>
    <body>
    <p>lighttpd for mips-linux</p>
    </body>
    </html>
### CGI配置

首先创建一个存放cgi程序的目录

```
mkdir -p /home/djstava/Downloads/lighttpd-1.4.35/install/webpages/cgi-bin
```

修改config/conf.d/cgi.conf

```
server.modules += ( "mod_cgi" ,
                    "mod_access"
                  )

##
## Plain old CGI handling
##
## For PHP don't forget to set cgi.fix_pathinfo = 1 in the php.ini.
##
cgi.assign                 = ( ".pl"  => "/usr/bin/perl",
                               ".cgi" => "",
                               ".rb"  => "/usr/bin/ruby",
                               ".erb" => "/usr/bin/eruby",
                               ".py"  => "/usr/bin/python" )
```

如上所写,以pl为扩展名的cgi程序会被perl来解析,其它的类似.

最后一步,修改conf/modules.conf

```
##
## plain old CGI (mod_cgi)
## 将cgi的配置文件包含进来
include "conf.d/cgi.conf"
```

准备一个用C语言编写的CGI程序

```
#include "stdio.h"

int main()
{
    printf("Content-Type: text/plain\n\n");
    printf("Hello CGI!\n");
    return 0;
}
```

编译一下

```
gcc hello.c -o hello.cgi
```

将生成的hello.cgi放到上面创建好的cgi-bin文件夹下,重启下lighttpd

```
killall lighttpd
./sbin/lighttpd -f conf/lighttpd.conf
```

一切就绪,在浏览器中访问下这个cgi

![lighttpd_cgi](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lighttpd/lighttpd_cgi_01.png)

### 在嵌入式设备上部署lighttpd(broadcom 7581)

* mkdir -p /home/djstava/Downloads/lighttpd-1.4.35/

* 将上面编译配置好的install文件夹copy到第一步中创建好的文件夹下

* 启动lighttpd

  	cd /home/djstava/Downloads/lighttpd-1.4.35/install    
  		sbin/lighttpd -f config/lighttpd.conf
* 测试 
  在pc浏览器中访问，如http://192.168.1.100/index.html,此处192.168.1.100是嵌入式设备的ip地址，请根据实际情况替换。

### 参考文献
1 <http://www.lighttpd.net/>   

2 <http://buffon.pixnet.net/blog/post/40142785-%5Bubuntu%5D-lighttpd-%2B-cgi>