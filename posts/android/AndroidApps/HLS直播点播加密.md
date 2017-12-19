### 软硬件环境

- ubuntu 16.04
- Android Studio 2.2.3
- OTT BOx with android 5.1.1
- nginx 1.11.3
- nginx-rtmp-module
- VLC

### 前言

在基于HLS的视频直播/点播应用中，为了保护自己的数字内容，防止被下载/拷贝/传播，需要给视频进行加密．本文完成服务器端加密及Android端的解密播放．

### nginx.conf

这里我把我使用的nginx.conf文件完整的列了出来，方便大家使用

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
        
        # exec ffmpeg -re -i udp://@224.0.0.1:4321 -vcodec libx264 -acodec aac -strict -2 -s 1280x720 -f flv rtmp://localhost/hls/livestream;

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
        application big {
            live on;

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
        }

        application small {
            live on;
            # Video with reduced resolution comes here from ffmpeg
        }

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
        
        application hls {
            live on;
            hls on;
            hls_path /opt/www/live;
            hls_continuous on;
            hls_sync 100ms;
            hls_nested on;
            hls_playlist_length 5m;
            hls_fragment 10s;

            hls_variant _low BANDWIDTH=800000;
            hls_variant _mid BANDWIDTH=1200000;
            hls_variant _hi  BANDWIDTH=2000000;

            #hls key
            hls_keys on;
            hls_key_path /opt/www/keys;
            hls_key_url http://10.10.10.79:8081/keys/;
            hls_fragments_per_key 10;

            #exec /home/djstava/Workshop/Web/nginx-1.11.3/build/test.sh;
            #exec_kill_signal term;

	        #recorder all {
	    	#    record all;
	        #    record_suffix -%Y-%m-%d-%H_%M_%S.flv;
		    #    record_max_size 6200000K;
	        #    record_path /opt/www/record;
	        #}
        }

	#application Upload {
	#    play /opt/www/record;
	#}

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

        location /control {  
            rtmp_control all;  
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

        location /dash {
            # Serve DASH fragments
            root /opt/www/;
            add_header Cache-Control no-cache;
        }
    }
}
```

主要是关注rtmp中的hls application

```
hls_keys on;
hls_key_path /opt/www/keys;
hls_key_url http://10.10.10.79:8081/keys/;
hls_fragments_per_key 10;
```

各个标签的含义如下

hls_keys : 是否启用加密

hls_key_path : 产生的key文件存放路径

hls_key_path : key文件的url，方便网络访问

hls_fragments_per_key : 共用同一个key的视频切片数

### 推流

以本地视频文件为例，nginx服务器ip为10.10.10.79

```
ffmpeg -re -i /opt/www/xjcy.mp4 -vcodec copy -acodec copy -f flv -y rtmp://10.10.10.79/hls/test
```

查看生成的key文件

![nginx_ffmpeg_key](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_08.png)

再注意看下生成的m3u8文件，默认加密方式是128位的AES

```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:357
#EXT-X-TARGETDURATION:19
#EXT-X-KEY:METHOD=AES-128,URI="http://10.10.10.79:8081/keys/test/354.key",IV=0x00000000000000000000000000000162
#EXTINF:11.679,
357.ts
#EXTINF:15.348,
358.ts
#EXTINF:19.395,
359.ts
#EXTINF:10.010,
360.ts
#EXTINF:10.010,
361.ts
#EXTINF:13.138,
362.ts
#EXTINF:13.305,
363.ts
#EXT-X-KEY:METHOD=AES-128,URI="http://10.10.10.79:8081/keys/test/364.key",IV=0x0000000000000000000000000000016C
#EXTINF:18.727,
364.ts
#EXTINF:16.350,
365.ts
#EXTINF:10.010,
366.ts
#EXTINF:10.010,
367.ts
#EXTINF:10.010,
368.ts
#EXTINF:17.225,
369.ts
#EXTINF:17.476,
370.ts
#EXTINF:15.223,
371.ts
#EXTINF:10.469,
372.ts
#EXTINF:10.010,
373.ts
#EXT-X-KEY:METHOD=AES-128,URI="http://10.10.10.79:8081/keys/test/374.key",IV=0x00000000000000000000000000000176
```

### Android客户端

基于vitamio 5.0.2版本的android播放器，可以直接播放http://10.10.10.79:8081/live/test/index.m3u8这个链接．我把整个工程打了个包，提供给需要的朋友．下载地址: <https://pan.baidu.com/s/1hsuSovy>

### 参考文献

* <https://github.com/arut/nginx-rtmp-module/wiki/Directives>
* http://www.xugaoxiang.com/2016/08/20/android-rtmp%E7%9B%B4%E6%92%AD/
* http://www.xugaoxiang.com/2016/11/03/android-rtmp%E7%9B%B4%E6%92%AD%E7%BB%AD/
* http://www.xugaoxiang.com/2016/11/08/android-rtmp%E7%9B%B4%E6%92%AD%E7%BB%AD%E4%BA%8C/

