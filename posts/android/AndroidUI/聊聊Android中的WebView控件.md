### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.4
* 坚果手机、Genymotion模拟器

### 前言
WebView是Android中用来显示网页内容的一个控件。随着前端技术的不断发展，WebView也被使用得越来越普遍，更有甚者，有的App只有一个WebView控件，所有的内容与事件响应都在服务器端中进行，这在Android TV中比较常见。

### 常规使用方法
要想使用WebView访问网页，需要在AndroidManifest.xml文件中加入网络访问的权限

	<uses-permission android:name="android.permission.INTERNET" />

在布局文件中声明WebView控件，属性在后面讲，这里是全屏显示

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">

	    <WebView
	        android:id="@+id/webView"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent" />
	</LinearLayout>

在Java文件中使用

	WebView webView = (WebView) findViewById(R.layout.webView);
	//打开对javascript的支持
	webView.getSettings().setJavaScriptEnabled(true);
	webView.loadUrl("http://blog.csdn.net/djstavav");

这样，网页就可以正常打开了，如下图

![][image-1]

### 常用XML属性
xml属性           |           详细说明
:----------------|:-----------------------------------------------
scrollbars    |  滚动条(horizontal、vertical、none)
focusableInTouchMode  |  view在触摸时是否能够获取焦点


### 显示本地页面
一般在页面访问中，不变的页面信息可以通过显示本地预存网页文件的形式来达到效果，而不需要去服务器端重新抓取，进而提升了app的性能。如常见的404错误页面。
首先准备一个html文件demo.html，这里仅显示一条简单的文本信息

	<html>
	    <body>
	        <script type="text/javascript">
	            document.write("<br/> <h1> ============= This is web page from local assets folder.============ </h1>");
	        </script>
	    </body>
	</html>

将上面的html文件存放在工程中的assets文件夹中，如果该文件夹不存在，请自行创建，然后利用WebView去访问此html文件

	webView.loadUrl("file:///android_asset/demo.html");

注意: “android\_asset”字段是固定要这样写的，显示的效果如下图

![][image-2]

### 显示加载进度
这里需要用到WebView的另一个方法setWebChromeClient，重写onProgressChanged方法

	webView.setWebChromeClient(new WebChromeClient() {
	    @Override
	    public void onProgressChanged(WebView view, int newProgress) {
	        super.onProgressChanged(view, newProgress);

	        setTitle("Loading ... " + newProgress + "%");
	        setProgress(newProgress * 100);
	        if (newProgress == 100) {
	            setTitle("Loading completely.");
	        }
	    }

	});

![][image-3]

### 页面缩放
双击放大，再次双击还原

	webView.getSettings().setBuiltInZoomControls(true);

PS: 如果要使用缩放功能，务必不要将WebView布局中的宽度或高度设置为wrap\_content

### 点击超级链接在当前WebView里跳转
如果按照上面的方法打开了我的博客首页，再点击某一篇博文时，会发现，新的页面会在系统的默认浏览器中打开，这显然不是我们想要看到的。如果想要在当前的WebView里打开页面，需要用到WebView的setWebViewClient方法，如下

	 webView.setWebViewClient(new WebViewClient() {
	     @Override
	     public boolean shouldOverrideUrlLoading(WebView view, String url) {
	         view.loadUrl(url);
	         return super.shouldOverrideUrlLoading(view, url);
	     }
	 });

### Android TV中WebView的按键响应问题
当布局中是一个WebView时，打开页面后，焦点就移到了WebView上，默认情况下，此时遥控器只响应返回键，如果需要响应其它键值(比如菜单键)，这时该怎么做呢？

一般的键值处理是放在onKeyDown(int keyCode,KeyEvent keyevent)里处理的，不过在上述场景中，onKeyDown方法中接收不到菜单键，如果想让其也能够接收到，也不难，往下看。

	@Override
	public boolean shouldOverrideKeyEvent(WebView webView,KeyEvent keyEvent) {
	    //让onKeyDown来处理KeyEvent.KEYCODE_STAR,此处以小红遥控器菜单键为例
	    if (keyEvent.getKeyCode() == KeyEvent.KEYCODE_STAR) {
	        return true;
	    } else {
	        return false;
	    }
	}

重写了上述方法以后，再去重写onKeyDown，捕捉对应的按键进行处理。

### 返回WebView的上一页面
需要重写onKeyDown方法，并捕捉返回键并判断，调用WebView的goBack方法

	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
	    if ((keyCode == KeyEvent.KEYCODE_BACK) && webview.canGoBack()) {
	        webview.goBack(); //goBack()表示返回WebView的上一页面
	        return true;
	    }
	    return false;
	}

### Javascript调用Java方法

关于这一点的实现，之前写过一篇博文，请参考

\<[http://blog.csdn.net/djstavav/article/details/48212083][1]\>

### 提升渲染优先级

设置渲染线程的优先级别，这个属性每个进程只需要被调用一次。在API level 22中已经被弃用。

	webView.getSettings().setRenderPriority(WebSettings.RenderPriority.HIGH);

### 不加载网络中的图片资源

在某些特殊情况下，可以设置不加载网页中的图片

	webView.getSettings().setBlockNetworkImage(true);

### 有效利用缓存

默认的cache目录是/data/data/包名/cache，可以通过getCacheDir方法来查看。

	webView.getSettings().setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);

优先从本地cache中载入，其次才是从网络中载入，即使cache已经过期。对应着Web Server Apache来查看，/var/log/apache2/access.log，可以看到，前后2次http请求同一张图片时的结果，第2次，http返回的code是304,表明资源并未从服务器端获取，而是从本地Pcache中获取。

![][image-4]

### 源码下载

\<[http://download.csdn.net/detail/djstavav/9213905][2]\>

### 参考文献
- [http://developer.android.com/intl/zh-cn/reference/android/webkit/WebView.html][3]
- [http://blog.csdn.net/djstavav/article/details/48212083][4]

[1]:	http://blog.csdn.net/djstavav/article/details/48212083
[2]:	http://download.csdn.net/detail/djstavav/9213905
[3]:	http://developer.android.com/intl/zh-cn/reference/android/webkit/WebView.html
[4]:	http://blog.csdn.net/djstavav/article/details/48212083

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/webview/WebViewThings_02.png "webview usage"
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/webview/WebViewThings_01.png "assets html file"
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/webview/WebViewThings_03.gif "loading"
[image-4]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/webview/WebViewThings_04.png "http 304"
