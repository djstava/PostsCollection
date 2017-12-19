### 软硬件环境

* ubuntu 16.04 64bit
* srs 2.0release

### SRS简介

SRS，即Simple-RTMP-Server，是一个开源的视频服务器，出自国人之手。release版本下载地址<https://github.com/ossrs/srs/releases>，目前的最高版本是v2.0-r2。

### 源码编译

```
tar xvf v2.0-r2.tar.bz2
cd srs-2.0-r2/trunk
mkdir build
./configure --prefix=/home/longjing/srs/srs-2.0-r2/trunk/build --with-http-api --with-hls --with-http-server --with-ssl --with-stat --with-research --with-dvr --with-http-callback
make 
make install
```

### 配置文件

```
listen              2935;
max_connections     1000;
daemon              on;
# srs_log_tank        console;

http_server {
    enabled         on;
    listen          8081;
    dir             ./objs/nginx/html;
}

vhost __defaultVhost__ {
    hls {
        enabled         on;
        hls_fragment    10;
        hls_window      60;
        hls_path        ./objs/nginx/html;
        hls_m3u8_file   [app]/[stream].m3u8;
        hls_ts_file     [app]/[stream]-[seq].ts;
        hls_cleanup     on;
        hls_dispose     30;
        # hls_on_error    disconnect;
        hls_storage     disk;
        hls_wait_keyframe       on;
        hls_acodec      aac;
        hls_vcodec      h264;
    }

    #http_hooks {
    #    enabled         on;
    #    on_connect      http://127.0.0.1:8085/api/v1/clients http://localhost:8085/api/v1/clients;
    #    on_close        http://127.0.0.1:8085/api/v1/clients http://localhost:8085/api/v1/clients;
    #    on_publish      http://127.0.0.1:8085/api/v1/streams http://localhost:8085/api/v1/streams;
    #    on_unpublish    http://127.0.0.1:8085/api/v1/streams http://localhost:8085/api/v1/streams;
    #    on_play         http://127.0.0.1:8085/api/v1/sessions http://localhost:8085/api/v1/sessions;
    #    on_stop         http://127.0.0.1:8085/api/v1/sessions http://localhost:8085/api/v1/sessions;
    #    on_dvr          http://127.0.0.1:8085/api/v1/dvrs http://localhost:8085/api/v1/dvrs;
    #}
}
```

### 启动服务

```
./objs/srs -c conf/http.hls.conf
```

### reload

```
killall -1 srs
```
### 参考资料

<https://github.com/ossrs/srs>

<http://www.ossrs.net/srs.release/releases/>