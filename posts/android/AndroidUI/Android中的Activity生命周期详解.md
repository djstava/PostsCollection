### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.2
* Genymotion模拟器

### 前言
在Android中，有四大开发组件，它们是Android编程的基础，Activity更是首当其冲，是重中之重。今天我们就来好好学习下Activity的生命周期。

### Activity是什么？
Activity是Android开发中最重要一个组件，几乎所有的交互操作都在Activity中进行，它主要负责创建窗口，在此窗口中你可以通过调用setContentView函数来将你需要的UI控件展现出来。先来看张Android API给出的一个activity生命周期图,如果一开始没看明白的，可以看完整篇博文再回头来看，加深理解。

![activity_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_01.png)

### 几个重要回调函数
以下的这些Activity方法展示了Activity的完整的生命周期，当Activity的状态发生变化时，可以通过重写这些方法来实现某些功能。

	 public class Activity extends ApplicationContext {
     	protected void onCreate(Bundle savedInstanceState);
     	protected void onStart();
     	protected void onRestart();
     	protected void onResume();
     	protected void onPause();
     	protected void onStop();
     	protected void onDestroy();
 	}

#### onCreate
当activity第一次被创建时调用。视图的创建、数据的初始化都在这里执行。眼尖的你可能已经发现，onCreate方法中有一个Bundle savedInstanceState参数，这里面会保存activity之前的状态，有很用。

#### onStart
会在onCreate方法后调用

#### onResume
会在onStart方法后调用。activity是使用栈结构来管理的，此时activity处于栈顶。

#### onRestart
被stop的activity重新启动时调用此函数，后面会紧跟onResume。

#### onPause
当有新的activity进入栈顶，即当前activity被迫到后台运行，onPause就会被调用。

#### onStop
会在onPause方法后调用，此时activity对于用户来讲，已经是不可见的了(Invisible)。从栈的角度来说，就是有新的activity压栈，或者已经存在的activity回到了栈顶。

#### onDestroy
这是生命周期中的最后一个函数。一般情况下activity已经完成(调用finish函数)或者系统为了回收资源主动销毁activity时，onDestroy才会被调用，可以通过isFinishing()方法来区分这2种情况。

综上，从onCreate开始到onDestroy结束，称为activity的一个完整生命周期。这里还有2个概念需要区别一下。

	1. 可见的(Visible，从onStart到onStop，这里的可见，不仅仅是在屏幕上可见)
	2. 前台运行(Foreground，从onResume到onPause)

为了弄明白这些回调函数在各种情况下的调用顺序，我们新建个工程，名叫ActivityLifecycle，在这些回调函数中加些打印，如下

	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = (EditText)findViewById(R.id.edittext);
        Log.d(TAG,"=== onCreate ===");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG,"=== onStart ===");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "=== onResume ===");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "=== onPause ===");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.d(TAG, "=== onRestart ===");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "=== onStop ===");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "=== onDestroy ===");
    }

运行一下程序，从Logcat中可以看到

![activity_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_02.png)

说明了一个Activity从创建到显示出来需要执行onCreate-->onStart-->onResume三个方法。

在App运行期间，如果想去在打开其它App，一般我们会去按Home键退出，这时候的回调又是怎样的呢？看下下图

![activity_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_03.png)

执行了onPause-->onStop，说明Activity并没有被销毁，只是被别的Activity覆盖了，被放到了后台去运行了，这种情形跟有突发事情发生(如有来电或者有App正在安装)是一样的，而当突发事件结束，Activity将会执行onRestart-->onStart-->onResume，整个过程打印如下图

![activity_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_06.png)

App运行时，如果当前Activity是最后一个Activity时，当我们按下Back键时，程序就会结束。这时候会相继执行onPause-->onStop-->onDestroy三个方法，如下图

![activity_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_04.png)

接下来稍微修改下工程，加入第2个Activity，叫SecondActivity，通过点击MainActivity上的一个按钮进入到SecondActivity，这时如果按下Back键，SecondActivity将会执行onPause-->onStop-->onDestroy，而MainActivity将会执行onRestart-->onStart-->onResume，如下图

![activity_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_05.png)

#### onSaveInstanceState与onRestoreInstanceState
这里有个概念，上边没有讲到。在onPause、onStop方法调用后，activity进程是有可能随时被杀掉的，所以应该在onPause方法中进行数据保存操作，以免数据丢失。onSaveInstanceState方法也是完成类似的功能，数据保存在给定的bundle里。因为onSaveInstanceState不是activity生命周期函数，android API更推荐在onPause方法中去保存数据。

#### 横竖屏切换
不做任何处理，切换下横竖屏，看看打印，可以发现先是销毁然后再重新创建了activity

![activity_07](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/activityLifecycle/activity_07.png)

为了解决上述问题，一般会有几个办法

* 禁止横竖屏切换(限定横屏或竖屏，比较流氓啊，不推荐)
* 横竖屏独立布局(创建2个文件夹layout-land和layout-port,对应的xml文件不变)
* 防止activity重载(在AndroidManifest.xml中添加android:configChanges="keyboardHidden|orientation"，并重写onConfigurationChanged方法)

### 源码下载
<http://download.csdn.net/detail/djstavav/9147793>

### 参考文献
1、<http://developer.android.com/intl/zh-cn/reference/android/app/Activity.html>    
2、<http://developer.android.com/guide/topics/manifest/activity-element.html>  
3、<http://developer.android.com/guide/topics/resources/runtime-changes.html>      
