### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.2
* Genymotion模拟器

### 前言
友盟是一款移动应用统计分析平台。它可以帮助移动应用运营人员统计和分析流量来源、产品留存数据、用户属性和行为数据等，以便产品开发者和运营人员利用数据进行产品、运营、推广策略的决策。

### 集成友盟SDK
首先到友盟官网<http://www.umeng.com/>注册个帐户，完成后到管理后台添加需要集成友盟统计的应用，如下

![umneg_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/umeng/umeng_01.png)

提交后，分给你的应用分配key，如下

![umneg_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/umeng/umeng_02.png)

接下来到<http://dev.umeng.com/analytics/android-doc/sdk-download>下载SDK，将下载下来的文件夹中的libs里的jar文件copy到工程中的libs中，在Android Studio中右键单击jar文件，选择Add as library完成导入。

#### 配置AndroidManifest.xml
添加相应权限，如下

	<uses-sdk android:minSdkVersion="4"></uses-sdk>
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	<uses-permission android:name="android.permission.INTERNET"></uses-permission>
	<uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>

然后填写key和渠道id，一个包中只能添加一个渠道，如下

	<meta-data android:value="这里填写友盟分配的key值" android:name="UMENG_APPKEY" />
    <meta-data android:value="这里填写渠道名称，如Wandoujia或者360" android:name="UMENG_CHANNEL" />

#### 集成代码
在每个Activity的onResume方法中调用MobclickAgent.onResume(Context)，在onPause方法中调用MobclickAgent.onPause(Context)。如果Activity之间有继承关系，不要重复添加onResume和onPause方法，否则会出现重复统计，影响统计结果。如果App中有调用Process.kill或者System.exit之类的方法杀死进程，请务必在此之前调用MobclickAgent.onKillProcess(Context)方法，用来保存统计数据。

至此，基本功能已经集成完毕，还是很简单的。后续的一些高级功能，可以根据自己的需求另行添加，官方的文档已经写得很详细了，这里就不再写了，感兴趣的去友盟的官网查看。


### 参考文献
1、<http://www.umeng.com/>    
2、<http://dev.umeng.com/analytics/android-doc/integration>    
