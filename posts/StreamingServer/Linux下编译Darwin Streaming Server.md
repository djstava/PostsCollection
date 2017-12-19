### 软硬件环境
* ubuntu 16.04
* Darwin Streaming Server 6.0.3

### 前言
Darwin Streaming Server是苹果公司开发的开源流媒体服务器，用C++语言编写，具有高性能、可扩展、模块化、跨平台等特性。

### 编译安装

#### 源码下载
目前最高版本是6.0.3，也是好久没有更新了

    http://dss.macosforge.org/

获取Linux平台的2个patch文件

    http://download.csdn.net/detail/djstavav/9612758

#### 编译

    tar xvf DarwinStreamingSrvr6.0.3-Source.tar
    cd DarwinStreamingSrvr6.0.3-Source
    mv ../dss-6.0.3.patch .
    mv ../dss-hh-20081021-1.patch .
    patch -p1 < dss-6.0.3.patch
    patch -p1 < dss-hh-20081021-1.patch
    ./Buildit install
    cd DarwinStreamingSrvr6.0.3-Linux
    sudo ./Install（这里会提示输入用户名及密码，在web管理中会使用到）

### 运行
安装成功后服务端程序自动运行，其它情况下也可以手动启动/usr/local/sbin/DarwinStreamingServer，如果想以debug模式启动，加入-d参数即可。服务端可供播放的视频文件存放于/usr/local/movies/,DSS配置文件是/etc/streaming/streamingserver.xml，日志文件存在于/var/streaming/logs，可登录http://192.168.95.134:1220(ip地址请自行修改)进行页面管理。 

### 测试
现在用vlc来测试下DSS是否安装成功

![dss_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_01.png)

![dss_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_02.png)

![dss_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_03.png)

![dss_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_04.png)

![dss_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_05.png)

![dss_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_06.png)

![dss_07](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_07.png)

![dss_08](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_08.png)

![dss_09](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/dss/dss_09.png)

### 参考文献
* http://dss.macosforge.org
* http://blog.sina.com.cn/s/blog_6a4c492f0100lkq3.html











