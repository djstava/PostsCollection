### 软硬件环境

- ubuntu 16.04 64bit
- Android Studio 2.2.3
- OTT BOx with android 5.1.1
- nginx 1.11.3
- nginx-rtmp-module

### 简介

电视回看是数字电视领域的另一项基础功能，它能提供一定时长的电视回放，可以实现像点播业务中的暂停，快进，快退等功能，一般的运营商都会提供回看的服务．本文讲述如何基于nginx-rtmp来实现电视回看．

### 原理

电视回看的核心是节目的录制．你可以选择与直播同步录制或者指定时间段录制，录制结束后生成节目资源的播放url，客户端拿到url进行播放，播放的过程和点播一样．

### 实践

##### nginx-rtmp配置文件

先来看看配置文件nginx.conf

```
#user  nobody;
worker_processes  auto;

rtmp_auto_push on;

error_log  logs/error.log debug;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

events {
    worker_connections  1024;
}

rtmp {

    server {

        listen 1935;

        chunk_size 4000;
        
        # HLS
        application hls {
            live on;
            hls on;
            hls_path /opt/www/live;
            hls_continuous on;
            hls_sync 100ms;
            hls_nested on;
            wait_key on;
            hls_playlist_length 5m;
            hls_cleanup on;
            hls_fragment 10s;

            # hls_keys on;
            # hls_key_path /opt/www/keys;
            # hls_key_url http://10.10.10.79:8081/keys;
            # hls_fragments_per_key 10;

	        recorder all {
	    	    record all manual;
	            record_suffix -%Y-%m-%d-%H_%M_%S.flv;
		        record_unique on;
	            record_path /opt/www/record;
	        }
        }
    }
}

# http
http {

    server {

        listen      8081;

	    location / {
	        root /opt/www/;
	    }

        # This URL provides RTMP statistics in XML
        location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl {
            root /home/djstava/Workshop/Web/nginx-rtmp-module/;
        }

        location /control {  
            rtmp_control all;  
        }

        location /hls {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /opt/www/;
            add_header Cache-Control no-cache;
        }

        location /dash {
            root /opt/www/;
            add_header Cache-Control no-cache;
        }
    }
}
```

主要关注下rtmp标签下的recorder和http标签下的control,录制的节目保存在/opt/www/record，/opt/www是nginx的根目录，便于http播放．

### 直播模拟

利用ffmpeg推送本地文件到服务器模拟直播

```
ffmpeg -re -i test.mp4 -vcodec copy -acodec copy -y -f flv rtmp://10.10.10.52/hls/live
```

### 节目录制

开始录制

```
curl "http://10.10.10.52:8081/control/record/start?app=hls&name=live&rec=all"
```

结束录制

```
curl "http://10.10.10.52:8081/control/record/stop?app=hls&name=live&rec=all"
```

录制完成后会在/opt/www/record目录下生成相应的flv文件

![nginx_rtmp_record](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/rtmp/rtmp_09.png)

### 客户端播放

播放需要的是url，把录制的文件名和主机nginx访问目录拼接一下，很简单，这里的话就是http://10.10.10.52:8081/record/-1483066808-2016-12-30-11_00_08.flv

### 参考文献

* https://github.com/arut/nginx-rtmp-module/wiki/Control-module


* http://www.xugaoxiang.com/2016/11/08/android-rtmp%E7%9B%B4%E6%92%AD%E7%BB%AD%E4%BA%8C/
* http://www.xugaoxiang.com/2016/11/03/android-rtmp%E7%9B%B4%E6%92%AD%E7%BB%AD/
* http://www.xugaoxiang.com/2016/08/20/android-rtmp%E7%9B%B4%E6%92%AD/

