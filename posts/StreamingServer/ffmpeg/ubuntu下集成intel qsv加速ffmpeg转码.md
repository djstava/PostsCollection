###  软硬件环境

* ubuntu 16.04 64bit
* ffmpeg binary 3.3.4
* intel mss 2017-r3
* Intel(R) Core(TM) i5-5200U CPU @ 2.20GHz 

### 准备工作

##### 安装MSS

intel 安装包下载地址

<https://software.intel.com/en-us/blogs/2017/07/11/whats-new-in-intel-media-server-studio-2017-r3>，下载community的版本，这个是免费的

```
tar xvf MediaServerStudioEssentials2017R3.tar.gz
cd MediaServerStudioEssentials2017R3
tar xvf SDK2017Production16.5.2.tar.gz
cd SDK2017Production16.5.2
cd Generic(我这里是ubuntu系统，如果是centos就进入CentOS目录)

# 安装libva
cd opt/intel/mediasdk/opensource/libva/1.67.0.pre1-64009
tar xvf libva-1.67.0.pre1.tar.bz2
cd libva-1.67.0.pre1
./configure
make 
sudo make install

# 安装libdrm
cd ../../../../../../../
cd opt/intel/mediasdk/opensource/libdrm/2.4.67-64009
tar xvf libdrm-2.4.67.tar.bz2
cd libdrm-2.4.67
./configure
make
sudo make install

cd ../../../../../../
tar xvf intel-linux-media_generic_16.5.2-64009_64bit.tar.gz
sudo install_media.sh
sudo reboot
```

在~/.bashrc中添加环境变量设置语句

```
LD_LIBRARY_PATH=/usr/local/lib:/usr/lib64
LIBVA_DRIVER_NAME=iHD
LIBVA_DRIVERS_PATH=/opt/intel/mediasdk/lib64
```

系统重启后，检查上述安装是否成功

```
djstava@ThinkPad:~$ lsmod | grep 'i915'
i915                 1208320  12
i2c_algo_bit           16384  2 i915,radeon
drm_kms_helper        155648  2 i915,radeon
drm                   364544  16 ttm,i915,drm_kms_helper,radeon
video                  40960  2 i915,thinkpad_acpi
```



```
djstava@ThinkPad:~/ffmpeg-3.3.4$ vainfo 
libva info: VA-API version 0.99.0
libva info: va_getDriverName() returns 0
libva info: User requested driver 'iHD'
libva info: Trying to open /opt/intel/mediasdk/lib64/iHD_drv_video.so
libva info: Found init function __vaDriverInit_0_32
libva info: va_openDriver() returns 0
vainfo: VA-API version: 0.99 (libva 1.67.0.pre1)
vainfo: Driver version: 16.5.2.64009-ubit
vainfo: Supported profile and entrypoints
      VAProfileH264ConstrainedBaseline:	VAEntrypointVLD
      VAProfileH264ConstrainedBaseline:	VAEntrypointEncSlice
      VAProfileH264ConstrainedBaseline:	<unknown entrypoint>
      VAProfileH264ConstrainedBaseline:	<unknown entrypoint>
      VAProfileH264Main               :	VAEntrypointVLD
      VAProfileH264Main               :	VAEntrypointEncSlice
      VAProfileH264Main               :	<unknown entrypoint>
      VAProfileH264Main               :	<unknown entrypoint>
      VAProfileH264High               :	VAEntrypointVLD
      VAProfileH264High               :	VAEntrypointEncSlice
      VAProfileH264High               :	<unknown entrypoint>
      VAProfileH264High               :	<unknown entrypoint>
      VAProfileMPEG2Simple            :	VAEntrypointEncSlice
      VAProfileMPEG2Simple            :	VAEntrypointVLD
      VAProfileMPEG2Main              :	VAEntrypointEncSlice
      VAProfileMPEG2Main              :	VAEntrypointVLD
      VAProfileVC1Advanced            :	VAEntrypointVLD
      VAProfileVC1Main                :	VAEntrypointVLD
      VAProfileVC1Simple              :	VAEntrypointVLD
      VAProfileJPEGBaseline           :	VAEntrypointVLD
      VAProfileJPEGBaseline           :	VAEntrypointEncPicture
      VAProfileVP8Version0_3          :	VAEntrypointEncSlice
      VAProfileVP8Version0_3          :	VAEntrypointVLD
      VAProfileVP8Version0_3          :	<unknown entrypoint>
      VAProfileVP9Profile0            :	<unknown entrypoint>
      <unknown profile>               :	VAEntrypointVideoProc
      VAProfileNone                   :	VAEntrypointVideoProc
      VAProfileNone                   :	<unknown entrypoint>
```

#####编译kernel

下载地址

<https://www.kernel.org/pub/linux/kernel/v4.x/>,下载4.4版本，这个版本号必须要和MSS里提供的kernel补丁版本对应

```
tar xvf linux-4.4.tar.gz
cd linux-4.4
cp -r MediaServerStudioEssentials2017R3/SDK2017Production16.5.2/Generic/opt/intel/mediasdk/opensource/patches/kmd/4.4/intel-kernel-patches .
for i in intel-kernel-patches/*.patch; do patch -p1 < $i; done
make olddefconfig
make
make modules_install 
sudo make install
sudo reboot
```

编译比较耗时，静静等待

#####编译安装ffmpeg

通过apt-get安装的ffmpeg本身不支持h264_qsv选项(windows版本支持)，因此必须自己去重新编译，首先安装必备的工具包

```
sudo apt-get install libvdpau-dev libvorbis-dev libass-dev libgnutls-dev frei0r-plugins-dev libopencore-amrwb-dev libopencore-amrnb-dev libopenjpeg-dev libopus-dev libsoxr-dev libvo-amrwbenc-dev libvpx-dev libwebp-dev libxvidcore-dev
```

在/opt/intel/mediasdk/include中创建文件夹mfx，并把include下的所有头文件拷贝到mfx文件夹中，然后

```
mkdir -p /opt/intel/mediasdk/lib64/pkgconfig
touch /opt/intel/mediasdk/lib64/pkgconfig/libmfx.pc
```

libmfx.pc文件内容如下

```
prefix=/opt/intel/mediasdk
exec_prefix=${prefix}
libdir=${prefix}/lib/lin_x64
includedir=${prefix}/include

Name: libmfx

Description: Intel Media Server Studio SDK
Version: 16.4.2
Libs: -L${libdir} -lmfx -lva -lstdc++ -ldl -lva-drm -ldrm
Cflags: -I${includedir} -I/usr/include/libdrm
```

最后一步，删除/usr/lib64下的所有libdrm\*和libva\*，我在这里被坑了很久，多个drm和va会导致ffmpeg编译时报错，如下

![ffmpeg_qsv_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/streaming/ffmpeg/ffmpeg_qsv_01.png)

ffmpeg 3.3.4源码包下载地址

<https://www.ffmpeg.org/download.html#get-sources>

```
tar xvf ffmpeg-3.3.4.tar.bz2
cd ffmpeg-3.3.4
./configure --enable-gpl --enable-version3 --enable-static --disable-debug --disable-indev=sndio --disable-outdev=sndio --enable-fontconfig --enable-frei0r --enable-gnutls --enable-gray --enable-libass --enable-libfreetype --enable-libfribidi --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenjpeg --enable-libopus --enable-librtmp --enable-libsoxr --enable-libspeex --enable-libtheora --enable-libvo-amrwbenc --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxvid
make
sudo make install
```

期间一般都会出现各种错误，通过查看config.log文件,依个解决．

### 测试

通过一下命令查看

```
djstava@ThinkPad:~/ffmpeg-3.3.4$ ffmpeg -codecs |grep qsv
ffmpeg version 3.3.4 Copyright (c) 2000-2017 the FFmpeg developers
  built with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.4) 20160609
  configuration: --enable-version3 --enable-libfdk-aac --enable-libmp3lame --enable-libx264 --enable-libmfx --extra-cflags=-I/opt/intel/mediasdk/include --extra-ldflags=-L/opt/intel/mediasdk/lib/lin_x64 --extra-libs=-lmfx --extra-libs=-ldl --enable-librtmp --enable-shared --enable-gpl --enable-postproc --enable-nonfree --enable-avfilter --enable-pthreads
  libavutil      55. 58.100 / 55. 58.100
  libavcodec     57. 89.100 / 57. 89.100
  libavformat    57. 71.100 / 57. 71.100
  libavdevice    57.  6.100 / 57.  6.100
  libavfilter     6. 82.100 /  6. 82.100
  libswscale      4.  6.100 /  4.  6.100
  libswresample   2.  7.100 /  2.  7.100
  libpostproc    54.  5.100 / 54.  5.100
 DEV.LS h264                 H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10 (decoders: h264 h264_qsv h264_vdpau h264_cuvid ) (encoders: libx264 libx264rgb h264_nvenc h264_qsv h264_vaapi nvenc nvenc_h264 )
 DEV.L. hevc                 H.265 / HEVC (High Efficiency Video Coding) (decoders: hevc hevc_qsv hevc_cuvid ) (encoders: nvenc_hevc hevc_nvenc hevc_qsv hevc_vaapi )
 DEV.L. mpeg2video           MPEG-2 video (decoders: mpeg2video mpegvideo mpegvideo_vdpau mpeg2_qsv mpeg2_cuvid ) (encoders: mpeg2video mpeg2_qsv mpeg2_vaapi )
 D.V.L. vc1                  SMPTE VC-1 (decoders: vc1 vc1_vdpau vc1_qsv vc1_cuvid )
 DEV.L. vp8                  On2 VP8 (decoders: vp8 vp8_cuvid vp8_qsv ) (encoders: vp8_vaapi )
```

### 参考资料

<http://blog.csdn.net/ww506772362/article/details/49865437>

<https://www.intel.com/content/www/us/en/architecture-and-technology/quick-sync-video/quick-sync-video-installation.html>

<https://github.com/Intel-Media-SDK/MediaSDK>

<https://trac.ffmpeg.org/wiki/Hardware/QuickSync>

<http://blog.csdn.net/leochen1983/article/details/72742656>