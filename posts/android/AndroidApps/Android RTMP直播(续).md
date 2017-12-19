### 软硬件环境
* ubuntu 16.04
* Android Studio 2.1.3
* OTT BOx with android 5.1.1
* nginx 1.11.3
* nginx-rtmp-module
* VLC

### 前言
之前的一篇博文<http://www.xugaoxiang.com/blog/index.php/archives/66/>已经简单的介绍了如何利用nginx、nginx-rtmp-module和ffmpeg实现基于rtmp协议的直播.今天这篇继续直播这个话题,聊聊hls的应用.

### HLS

HLS(Http Live Streaming)是由Apple公司定义的用于实时流传输的协议，HLS基于HTTP协议实现，传输内容包括两部分，一是M3U8描述文件，二是TS媒体文件。

##### m3u8文件

```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:6119
#EXT-X-TARGETDURATION:14
#EXTINF:10.625,
6119.ts
#EXTINF:13.667,
6120.ts
#EXTINF:10.000,
6121.ts
```

如上,m3u8文件是一个描述文件,必须以#EXTM3U开头,之后是切片TS文件的序列.对于直播来讲,m3u8文件需要进行实时的更新,只保留若干个TS切片序列,防止本地存储撑爆硬盘.

##### 多码率支持

针对应用网络多变及不稳定的情况,多数直播都会提供多码率支持,播放器会根据用户当前的网络状况,自动切换到对应的码率上,大大提升用户体验.在服务器端.为了提供多码率的支持,就需要多级m3u8文件.在主m3u8文件不再有TS序列,而是二级m3u8文件,如下所示

```
#EXTM3U
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=1280000
low.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=2560000
mid.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=7680000
hi.m3u8
```

### nginx-rtmp对HLS的支持

nginx-rtmp-module本身对rtmp和hls都有很好的支持,只需要在nginx.conf配置下就ok了

```

#user  nobody;
worker_processes  auto;

rtmp_auto_push on;

error_log  logs/error.log;
error_log  logs/error.log  notice;
error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

rtmp {

    server {

        listen 1935;

        chunk_size 4000;

        # TV mode: one publisher, many subscribers
        #application mytv {

            # enable live streaming
            #live on;

            # record first 1K of stream
            #record all;
            #record_path /tmp/av;
            #record_max_size 1K;

            # append current timestamp to each flv
            #record_unique on;

            # publish only from localhost
            #allow publish 127.0.0.1;
            #deny publish all;

            #allow play all;
        #}

        # Transcoding (ffmpeg needed)
        #application big {
        #    live on;

            # On every pusblished stream run this command (ffmpeg)
            # with substitutions: $app/${app}, $name/${name} for application & stream name.
            #
            # This ffmpeg call receives stream from this application &
            # reduces the resolution down to 32x32. The stream is the published to
            # 'small' application (see below) under the same name.
            #
            # ffmpeg can do anything with the stream like video/audio
            # transcoding, resizing, altering container/codec params etc
            #
            # Multiple exec lines can be specified.

        #    exec ffmpeg -re -i rtmp://localhost:1935/$app/$name -vcodec flv -acodec copy -s 32x32
                        #-f flv rtmp://localhost:1935/small/${name};
        #}

        #application small {
        #    live on;
        #    # Video with reduced resolution comes here from ffmpeg
        #}

        #application webcam {
        #    live on;

            # Stream from local webcam
        #    exec_static ffmpeg -f video4linux2 -i /dev/video0 -c:v libx264 -an
                               #-f flv rtmp://localhost:1935/webcam/mystream;
        #}

#        application mypush {
#            live on;

            # Every stream published here
            # is automatically pushed to
            # these two machines
            #push rtmp1.example.com;
            #push rtmp2.example.com:1934;
#        }

#        application mypull {
#            live on;

            # Pull all streams from remote machine
            # and play locally
            #pull rtmp://rtmp3.example.com pageUrl=www.example.com/index.html;
#        }

#        application mystaticpull {
#            live on;

            # Static pull is started at nginx start
            #pull rtmp://rtmp4.example.com pageUrl=www.example.com/index.html name=mystream static;
#        }

        # video on demand
#        application vod {
#            play /opt/www/vod;
#        }

#        application vod2 {
#            play /var/mp4s;
#        }

        # Many publishers, many subscribers
        # no checks, no recording
        #application videochat {

         #   live on;

            # The following notifications receive all
            # the session variables as well as
            # particular call arguments in HTTP POST
            # request

            # Make HTTP request & use HTTP retcode
            # to decide whether to allow publishing
            # from this connection or not
         #   on_publish http://localhost:8080/publish;

            # Same with playing
         #   on_play http://localhost:8080/play;

            # Publish/play end (repeats on disconnect)
         #   on_done http://localhost:8080/done;

            # All above mentioned notifications receive
            # standard connect() arguments as well as
            # play/publish ones. If any arguments are sent
            # with GET-style syntax to play & publish
            # these are also included.
            # Example URL:
            #   rtmp://localhost/myapp/mystream?a=b&c=d

            # record 10 video keyframes (no audio) every 2 minutes
          #  record keyframes;
          #  record_path /tmp/vc;
          #  record_max_frames 10;
          #  record_interval 2m;

            # Async notify about an flv recorded
          #  on_record_done http://localhost:8080/record_done;

        #}


        # HLS

        # For HLS to work please create a directory in tmpfs (/tmp/hls here)
        # for the fragments. The directory contents is served via HTTP (see
        # http{} section in config)
        #
        # Incoming stream must be in H264/AAC. For iPhones use baseline H264
        # profile (see ffmpeg example).
        # This example creates RTMP stream from movie ready for HLS:
        #
        # ffmpeg -loglevel verbose -re -i movie.avi  -vcodec libx264
        #    -vprofile baseline -acodec libmp3lame -ar 44100 -ac 1
        #    -f flv rtmp://localhost:1935/hls/movie
        #
        # If you need to transcode live stream use 'exec' feature.
        #
        application hls {
            live on;
            hls on;
            hls_path /opt/www/live;
        }

        # MPEG-DASH is similar to HLS

        #application dash {
        #    live on;
        #    dash on;
        #    dash_path /tmp/dash;
        #}
    }
}

# HTTP can be used for accessing RTMP stats
http {

    server {

        listen      8081;

	    location / {
	        root /opt/www/;
	    }

        # This URL provides RTMP statistics in XML
        location /stat {
            rtmp_stat all;

            # Use this stylesheet to view XML as web page
            # in browser
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl {
            # XML stylesheet to view RTMP stats.
            # Copy stat.xsl wherever you want
            # and put the full directory path here
            root /home/djstava/Workshop/Web/nginx-rtmp-module/;
        }

        location /hls {
            # Serve HLS fragments
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /opt/www/;
            add_header Cache-Control no-cache;
        }

        #location /dash {
            # Serve DASH fragments
        #    root /tmp;
        #    add_header Cache-Control no-cache;
        #}
    }
}
```

在rtmp标签下,指定hls application的根路径/opt/www/live,所有的TS切片文件都存放在这里

### ffmpeg推流

##### 推送本地文件

```
ffmpeg -re -i /opt/www/vod/dhxy1.mp4 -vcodec copy -acodec copy -f flv -y rtmp://192.168.1.88/hls/livestream1
```

推送成功后,你可以通过如下2个url播放对应的模拟实时流,请确保nginx服务已启动.

```
rtmp://192.168.1.88/hls/livestream1
http://192.168.1.88:8081/live/livestream1.m3u8
```

另外http://192.168.1.88:8081/stat页面可以显示当前服务的一些信息,如接入的客户端数量,音频 视频的信息等等,见下图

![nginx_stat](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_03.png)

#####  推送UDP组播数据

```
ffmpeg -i udp://@224.0.0.2:9000 -vcodec libx264 -acodec aac -strict -2 -f flv -s 1280x720 -q 10 -ac 1 -ar 44100 rtmp://192.168.1.88/hls/livestream
```

![nginx_udp](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_04.png)

在以UDP数据为输入源时,ffmpeg会报如下图中的错误信息

![nginx_udp_error](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_05.png)

这时只需要重新修改下ffmpeg的推流命令就可以,如下

```
ffmpeg -i 'udp://@224.0.0.2:9000?fifo_size=2000000&overrun_nonfatal=1' -vcodec libx264 -acodec aac -strict -2 -f flv -s 1280x720 -q 10 -ac 1 -ar 44100 rtmp://192.168.1.88/hls/livestream
```

fifo_size的单位是字节,自己酌情增减.

### 参考文献

1 <https://developer.apple.com/streaming/>

2 <https://github.com/arut/nginx-rtmp-module>