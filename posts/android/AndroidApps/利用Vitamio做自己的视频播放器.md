### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.1
* 酷比魔方7寸平板

### 前言
Vitamio是一款Android和iOS平台上的多媒体开发框架，支持硬件解码与GPU渲染。它的应用很广，很多知名的APP都在使用它。Vitamio目前分为标准版和至尊版，标准免费版本仅限个人开发者使用，非个人移动应用均需要购买使用授权。

### 准备工作
到Vitamio的Github地址 <https://github.com/yixia/VitamioBundle>下载SDK

在Android Studio中新建一个工程名为DJMediaPlayer,再把下载下来的vitamioBundle目录底下的vitamio文件夹以模块的形式导入到新建的工程当中,接着将vitamio模块下的build.gradle文件中的compileSdkVersion、buildToolsVersion、minSdkVersion和targetSdkVersion都修改成跟app模块下的build.gradle相应字段一样的数据。

![djmediaplayer_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmediaplayer/djmediaplayer_01.png)

最后修改下app模块下build.gradle,增加依赖语句

![djmediaplayer_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmediaplayer/djmediaplayer_02.png)

### 开始编码

#### 布局
总共分2块，第一是视频列表布局，这里设计成一个listview，另一个是视频播放时的布局，这里用到了vitamio里的io.vov.vitamio.widget.VideoView控件，直接用来播放视频，另外通过手指滑动来控制音量大小则用的是FrameLayout。

#### 扫描SD视频文件
这个的做法跟<http://blog.csdn.net/djstavav/article/details/47726675>里的音乐扫描是一样的

#### 播放器的代码
这时候该轮到vitamio上场了，直接用它提供的VideoView和MediaController

	videoView = (VideoView)findViewById(R.id.surface_view);
	mediaController = new MediaController(this);

	//设置成撑满全屏
	videoView.setVideoLayout(VideoView.VIDEO_LAYOUT_STRETCH,0);
	videoView.setMediaController(mediaController);
	videoView.requestFocus();

	videoView.setVideoPath(movieUrl);

除此之外，VideoView的setOnPreparedListener、setOnCompletionListener、setOnErrorListener都需要实现一下。

#### 播放器上的手势控制
这里只实现了3个手势。分别对应手势类GestureDetector的三个方法，即onDoubleTap、onScroll和onFling。
第1个手势是双击屏幕，实现播放/暂停功能。这个要重写onDoubleTap方法

	if (videoView.isPlaying()) {
    	videoView.pause();
    } else {
       videoView.start();
    }
第2个手势是水平滑动屏幕，实现快进/快退功能。这个要重写onFling方法

	currentPosition = videoView.getCurrentPosition();

	  /*向左滑*/
	  if (e1.getX() - e2.getX() > 120) {

	      if (currentPosition < 10000) {
	          currentPosition = 0;
	          videoView.seekTo(currentPosition);
	      } else {
	          videoView.seekTo(currentPosition - 10000);
	      }

	  } else if (e2.getX() - e1.getX() > 120) {
	  /*向右滑*/
	      if (currentPosition + 10000 > duration) {
	          currentPosition = duration;
	          videoView.seekTo(currentPosition);
	      } else {
	          videoView.seekTo(currentPosition + 10000);
	      }

	  }

第3个手势是上下滑动屏幕，实现音量的升降功能。这个要重写onScroll方法

	   //上下滑动
	  if (Math.abs(distanceY) / Math.abs(distanceX) > 3) {
	      float mOldY = e1.getY();
	      int y = (int) e2.getRawY();
	      Display disp = getWindowManager().getDefaultDisplay();
	      int windowHeight = disp.getHeight();

	      onVolumeSlide((mOldY - y) / windowHeight);
	  }

![djmediaplayer_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmediaplayer/djmediaplayer_05.png)	 

![djmediaplayer_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmediaplayer/djmediaplayer_03.png)

![djmediaplayer_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmediaplayer/djmediaplayer_04.png)

### 备注
发现在工程清单文件中增加SD卡的权限时

	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

Android Studio在自动补全时，android.permission这些字符全是大写，导致程序跑起来达不到自己想要的效果，这里要注意，要写成小写。

### 源码下载

Github:     
	<https://github.com/djstava/DJMediaPlayer>

### 参考文献
<https://vitamio.org/>
