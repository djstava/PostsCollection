### 软硬件环境
* ubuntu 16.04
* Android Studio 2.1.3
* OTT BOx with android 5.1.1
* nginx 1.11.3
* nginx-rtmp-module
* vitamio

### 前言
当下,直播已经成为网络热词,它不单单是指传统广播电视的实时播放,更是更为广泛的音视频实时分享的延伸。早先,直播数据源只可能来自于电视台及节目制作中心,但是放眼现在,基于计算机技术的高速发展,任何人都可以独自完成内容的制作,再利用身边的终端设备完成分享,你不仅仅是受众,同样可以成为主角。可以说,时下流行的"网红"文化,直播技术立下了汗马功劳。本文旨在搭建一个最简单的视频直播系统,包括服务器端及android客户端,采用了nginx、nginx-rtmp、vitamio及ffmpeg。

### rtmp协议
RTMP是Real Time Messaging Protocol的缩写,是被设计用来进行实时数据通信的网络协议。它是一个协议族,包括rtmpe、rtmpt、rtmps等,是直播技术中常用的协议

### 服务端配置
##### nginx添加rtmp支持
从http://nginx.org/en/download.html下载最新版1.11.3,然后从https://github.com/arut/nginx-rtmp-module下载nginx的rtmp补丁，下载的文件都放在目录/home/djstava(请根据实际情况自行修改)下，重新编译nginx

    tar xvf nginx-1.11.3.tar.gz
    cd nginx-1.11.3
    mkdir build
    ./configure --prefix=/home/djstava/nginx-1.11.3/build --add-module=/home/djstava/nginx-rtmp-module
    make
    make install

##### 修改配置文件nginx.conf
编辑/home/djstava/nginx-1.11.3/build/conf/nginx.conf,在文件末尾添加如下内容

    rtmp {
        server {
            listen 1935;
            ping 30s;
            notify_method get;
    
            application myapp {
                live on;
    
                # sample play/publish handlers
                #on_play http://localhost:8080/on_play;
                #on_publish http://localhost:8080/on_publish;
    
                # sample recorder
                #recorder rec1 {
                #    record all;
                #    record_interval 30s;
                #    record_path /tmp;
                #    record_unique on;
                #}
    
                # sample HLS
                #hls on;
                #hls_path /tmp/hls;
                #hls_sync 100ms;
            }
    
            # Video on demand
            #application vod {
            #    play /var/Videos;
            #}
    
            # Video on demand over HTTP
            #application vod_http {
            #    play http://localhost:8080/vod/;
            #}
        }
    }

##### 启动nginx服务
    /home/djstava/nginx-1.11.3/build/sbin/nginx
    
##### ffmpeg推送rtmp
找一个本地的视频文件进行推送，命令为
    
    ffmpeg -re -i 大话西游之月光宝盒.BD1280超清国粤双语中英双字.mp4 -c copy -f flv rtmp://localhost/myapp/mystream

##### ffplay播放测试
如果没有安装ffplay的话，也可以用vlc
    
    ffplay rtsmp://localhost/myapp/mystream
    
##### 后记
前面的步骤都是在本机中进行的。可是在实际应用中，情况会复杂的多，nginx可能是一台服务器，ffmpeg推流的可能就是另一台服务器，这样的话，可将localhost换成对应的IP地址。如果数据源来自摄像头，同样可以通过ffmpeg进行推送，命令如下

    ffmpeg -f dshow -i video="Integrated Camera" -vcodec libx264 -preset:v ultrafast -tune:v zerolatency -f flv rtmp://10.10.10.84/myapp/mystream1
    
![rtmp_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_02.png)

### Android客户端播放
之前已经写过一个基于vitamio的视频播放器，地址是https://github.com/djstava/DJMediaPlayer，我们就在它的基础上进行修改,找到MainActivity.java

    private String[] files = {"rtmp demo","apple demo"};
   
在listview的item被点击后发送包含播放地址的intent

    Intent intent = new Intent(MainActivity.this, VitamioVideoViewActivity.class);
    intent.putExtra("movieUrl", "rtmp://10.10.10.84/myapp/mystream");
    startActivity(intent);
    
![rtmp_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_01.png)

### 参考文献
* https://ffmpeg.org/download.html
* https://github.com/yixia/VitamioBundle
* https://github.com/djstava/DJMediaPlayer
* http://nginx-rtmp.blogspot.com