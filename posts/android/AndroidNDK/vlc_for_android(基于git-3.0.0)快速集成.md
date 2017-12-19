### 软硬件环境

* ubuntu 16.04 64bit
* Android 5.1.1 TV Box
* Android studio 2.3.1

### 前言

最近老有朋友在问vlc for android的移植问题，网络上有一些帖子也有谈到，但是大部分都是基于老版本，况且vlc的更新也很频繁，接口也经常变动，给大家造成了很大的困扰。鉴于此，我专门写一篇博文，将具体的方法及步骤写出来，最后将源码工程上传到了github，方便一些有此需求的朋友，如果感觉对你有所帮助，请去github点个赞，地址是<https://github.com/djstava/OTTLivePlayer_vlc>，有什么问题也可以在issue里提，谢谢。

### vlc-android源码编译

我们的目的是使用最新的vlc-android，而且随着vlc的更新升级，我们的app也需要随时能够得到更新，因此编译源码是少不了的。这个我之前有一篇博文已经写过，请参考<http://www.xugaoxiang.com/blog/index.php/archives/57/>

### vlc-android代码剥离

vlc-android编译完成后 ，目录文件很多。我们需要将动态链接库so文件和java层的相关类库拷出来，它们的路径分别如下图所示

![vlc_dir](https://raw.githubusercontent.com/djstava/OTTLivePlayer_vlc/master/images/jniso.png)



![jniso2](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/jniso_2.png?raw=true)



![vlc_java](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/vlc_3.png?raw=true)



### 如何创建MediaPlayer

```
ArrayList<String> options = new ArrayList<>();
options.add("-vvv");
libvlc = new LibVLC(this, options);

mediaPlayer = new MediaPlayer(libvlc);

IVLCVout ivlcVout = mediaPlayer.getVLCVout();
ivlcVout.setVideoView(surfaceView);
ivlcVout.attachViews();

Media media = new Media(libvlc, Uri.parse("udp://@225.0.0.1:9000"));
mediaPlayer.setMedia(media);
mediaPlayer.play();
```

### 一些坑

##### Error:(466, 73) 错误: -source 1.6 中不支持 diamond 运算符 (请使用 -source 7 或更高版本以启用 diamond 运算符)

Android studio --> File --> Project structure... --> app --> Source Compatibility

![e1](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/q1.png?raw=true)

### 工程截图

![s1](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/screencap_1.png?raw=true)



![s2](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/screencap_2.png?raw=true)



![s3](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/screencap_3.png?raw=true)

![s4](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/screencap_4.png?raw=true)

![s5](https://github.com/djstava/OTTLivePlayer_vlc/blob/master/images/screencap_5.png?raw=true)

### 参考资料

1 <https://github.com/djstava/OTTLivePlayer_vlc>

2 <http://www.xugaoxiang.com/blog/index.php/archives/57/>

3 <https://wiki.videolan.org/AndroidCompile>

