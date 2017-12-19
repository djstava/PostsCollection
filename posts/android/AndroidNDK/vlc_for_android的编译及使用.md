### 软硬件环境
* ubuntu 16.04 64bit
* Android 5.1.1 TV Box
* Android studio 2.1.2

### 概述
VLC是一款大名鼎鼎的流媒体播放器,对本地和流式媒体都支持良好,跨平台,支持当前市面上绝大多数的封装格式及编码格式,最重要的,它还是开源软件.

### Android SDK和NDK环境
之前一直有用Android Studio工具,所以这个环境都已经配置好了,在工程配置文件local.properties中会有描述.

    ndk.dir=/home/djstava/Workshop/tool/SDK/ndk-bundle
    sdk.dir=/home/djstava/Workshop/tool/SDK

如果之前压根就没有配过,那就从头开始,安装Android Studio,再通过android studio分别去安装SDK和NDK.拿到了这2个路径,设置到环境变量ANDROID_SDK和ANDROID_NDK中去.

    export ANDROID_NDK=/home/djstava/Workshop/tool/SDK/ndk-bundle
    export ANDROID_SDK=/home/djstava/Workshop/tool/SDK
    export PATH=$PATH:$ANDROID_SDK/platform-tools:$ANDROID_SDK/tools

建议将上述配置语句写入~/.bashrc文件中,免得每次编译都要去手动配置.

### 安装必备的系统工具包
首先安装一些必备的系统工具,如git jdk8 make等

    sudo apt-get install automake ant autopoint cmake build-essential libtool \
 patch pkg-config protobuf-compiler ragel subversion unzip git openjdk-8-jdk

### 编译VLC源码
准备工作就绪,现在开始本文的重头戏.首先还是需要拉下源码

    git clone https://code.videolan.org/videolan/vlc-android.git
    cd vlc-android
    sh compile.sh

##### compile.sh做了些什么?
查看compile.sh文件,知道脚本会去下载 配置gradle环境(目前的版本是2.14.1),如果执行这个脚本之前跟我一样没有设置ANDROID_ABI环境变量,编译的目标架构就是ARMv7.要修改的话就添加-a参数

    sh compile.sh -a <ABI>

其中<ABI>的可选值有

    * armeabi-v7a
    * arm64-v8a
    * x86
    * x86_64
    * MIPS
    * MIPS64

gradle配置的最后会将ANDROID_SDK和ANDROID_NDK的值写入到vlc-android下的local.properties文件中,将app签名相关的信息写入gradle.properties文件中.

脚本的最后是去下载vlc的源码,然后调用另一个脚本文件compile-libvlc.sh去编译.

##### compile-libvlc.sh做了些什么?
这是最核心的部分,它负责对vlc进行configure和make,这个过程和在Linux系统下去编译软件是一样的,所不同的是这里用的是android NDK的工具集去生成jni编程中需要用到的动态链接库.做纯app的童鞋,可能之前都没有接触过这块,不过不要紧.也许之前你只是单纯的去调用so,现在这部分我们也做了.脚本执行过程中会去下载需要的工具包,请保持网络畅通.

在播放过程中遇到的很多问题,基本上都是要从这一块来找原因,可想而知这部分的重要性了.

漫长的等待(机器性能差)后,apk终于生成了,位于./vlc-android/build/outputs/apk/VLC-Android-2.1.0-ARMv7.apk

##### 后续的软件升级
前面都是采用git的方式下载源码,因此软件升级就变得非常容易了,git pull再编译就可以了,这里就不多说了.



### 源码下载

[vlc-android](http://pan.baidu.com/s/1qX9jV5a)

### 参考文献
1. https://wiki.videolan.org/AndroidCompile
2. http://www.androiddevtools.cn
