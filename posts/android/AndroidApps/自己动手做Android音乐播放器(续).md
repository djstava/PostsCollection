### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.2
* 酷比魔方7寸平板

### 前言
前面一篇博文<http://blog.csdn.net/djstavav/article/details/47726675>已经简单实现了一个Android音乐播放器的基本功能，现在在之前工作的基本上，再加上文件选择的功能。

这张图中既有文件夹又有单个的音乐文件

![djmusic_plus_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmusic/djmusic_plus_04.png)

这张图中目录下全是音乐文件

![djmusic_plus_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djmusic/djmusic_plus_02.png)

### 开发思路

1 将SD卡根目录作为默认初始显示目录    
2 将文件夹及音乐文件挑选出来    
3 按字母升序排序    
4 启动music service，并将当前目录下的音乐列表传递过去    
5 进入下一层子目录时，重复第2、3步操作    
6 返回时切换到上一层目录直到SD卡根目录

### 工程源码
<https://github.com/djstava/DJMusic>
