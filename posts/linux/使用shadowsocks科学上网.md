### 软硬件环境

* ubuntu server 16.04 
* shadowsocks client 2.5.2

### 前言

最近在https://bwh1.net/(俗称搬瓦工)上买了VPS服务，就想着换种方法科学上网，之前都用的是付费VPN，因为种种原因，经常被封、无法使用。shadowsocks时最常使用的工具，安装使用也非常简单方便。

### 安装shadowsocks服务

```
apt-get install python-pip
apt-get install shadowsocks
```

### 编辑配置文件

/etc/shadowsocks.json

```
{
"server":"*.*.*.*",
"server_port":9999,
"local_address": "127.0.0.1",
"local_port":1080,
"password":"123456",
"timeout":300,
"method":"aes-256-cfb ",
"fast_open": false,
"workers": 1
} 
```

其中，server是vps分配给你的IP地址，server_port可以随便设，别跟其它服务端口重复就行, password是客户端登录是需要填写的密码，method是加密方法

### 启动shadowsocks服务

```
ssserver -c /etc/shadowsocks.json
```

可以将启动命令写入/etc/rc.local让其开机自动开启服务。

### 安装shadowsocks客户端

以windows为例，下载后文中提供的客户端，打开配置界面

![shadowsocks_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/ts/shadowsocks/shadowsocks_01.png)

设置完毕后，启动系统代理，可以愉快的上网了。

### 福利

最后给个搬瓦工的打折码，给需要的朋友，6%折扣。<BWH1ZBPVK>

### 各平台客户端下载

<https://pan.baidu.com/s/1jI9ubdG>