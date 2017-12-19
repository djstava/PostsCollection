### 软硬件环境
* ubuntu 16.04
* EasyDarwin latest

### 前言
EasyDarwin是基于Apple开源项目Darwin Streaming Server开发的流媒体服务器，支持RTSP点播、直播（推拉模式）、HLS直播等功能；经过几年的发展，项目已经非常稳定，而且相关扩展的项目也很多，形成了自己的项目族，具体可参考他们的官方网站http://www.easydarwin.org

### 编译安装

#### 下载编译

    git clone https://github.com/EasyDarwin/EasyDarwin
    cd EasyDarwin
    chmod a+x Buildit
    ./Buildit i386(或者x64，根据你的平台决定)
   
#### 运行服务
源码编译完成后，会在i386目录下生成可执行文件easydarwin，默认的配置文件在WinNTSupport/easydarwin.xml

    ./easydarwin -c WinNTSupport/easydarwin.xml -d

easydarwin.xml包含了N多的配置项，你可以根据自己的需要进行修改。目前的话，你只需要知道视频存放路径movie_folder,默认值是Movies

#### 备注
普通的mp4文件如果直接丢到Movies目录下，vlc是无法进行rtsp点播的。视频文件需要先借助第三方工具进行RTSP/RTP hint处理，这里推荐My MP4Box GUI,下载地址http://www.videohelp.com/software/My-MP4Box-GUI，视频处理非常简单，这里就不多说了。

![easy_0](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/easyDarwin/easy_01.png)

#### 相关错误码
响应吗	|   报文描述	| 定义
:-----------------------|:---------------------
200 | Success OK　成功
201	| Success Created 成功创建
202	| Success Accepted 已接受用于处理，但处理尚未完成
204	| Success No Content 已接收请求，但不存在要回送的信息
206	| Success Partial Content 已接收请求，但要回送的信息不完整
301	| Redirect Permanent Moved 请求的数据具有新的位置且更改是永久的。
302	| Redirect Temp Moved	请求的数据临时具有不同 URI
303	| Redirect See Other 可在另一 URI 下找到对请求的响应
305	| Use Proxy	必须通过位置字段中提供的代理来访问请求的资源
400	| Client Bad Request 请求中有语法问题，或不能满足请求
401	| Client Unauthorized 未授权客户端访问数据
402	| Payment Required 需要付款,表示计费系统已有效
403	| Client Forbidden 禁止, 即使有授权也不需要访问
404	| Not Found	服务器找不到给定的资源
405	| Method Not Allowed 请求的方法不支持
407	| Proxy Authentication Required	代理认证请求，客户机首先必须使用代理认证自身
408	| Request Timeout 请求超时
409	| Conflict 请求冲突
412	| Precondition Failed 前提条件失败
415	| Unsupported Media Type 服务器拒绝服务请求，因为不支持请求实体的格式
500	| Server Internal Error	内部错误,因为意外情况，服务器不能完成请求
501	| Server Not Implemented 未执行,服务器不支持请求
502	| Server Bad Gateway 错误网关,服务器接收到来自上游服务器的无效响应
503	| Server Unavailable 由于临时过载或无法获得服务护,服务器无法处理请求
505	| RTSP Version Not Supported 不支持的RTSP版本
600	| Memcache Not Found 找不到Memcache服务器
601	| Database Not Found 找不到Database 服务器
602	| User Not Found 找不到用户信息（该用户不存在）
603	| Device Not Found	找不到设备信息（该设备不存在或者没有与请求用户绑定）
604	| Session Not Found	找不到会话信息(Session过期或者不存在)
605	| Service Not Found	找不到请求的服务模块
620	| Password Error 密码错误
621	| XML Parse Error XML解析失败
622	| Permission Error 没有权限

### 摄像头作为视频源进行rtsp直播
服务器端利用ffmpeg抓取摄像头数据，然后推送到easyDarwin

    ffmpeg -f video4linux2 -s 720x576 -i /dev/video0 -f audio_device -f rtsp rtsp://127.0.0.1/live/test.sdp
    
客户端利用vlc播放

    vlc rtsp://127.0.0.1:554/live/test.sdp
    
![easy_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/easyDarwin/easy_02.png)

### 参考文献
* http://www.easydarwin.org/
* http://www.360doc.com/content/11/0126/18/2633_89188287.shtml
* http://www.easydarwin.org/article/EasyDarwin/43.html