### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.4
* 酷比魔方7寸平板
* 百度导航SDK 3.0.0

### 运行导航Demo
首先还是常规路数，申请开发者账户，创建应用

![Baidu Dev Center][image-1]

接下来去下载Demo工程，地址是 [http://lbsyun.baidu.com/sdk/download][1]，这里最好是自己新建一个android工程，然后将demo中的相应文件拷贝到工程里去，因为在申请Key的过程中就需要用到包名。修改工程菜单文件AndroidManifest.xml文件，将申请的Key添加进去，重新运行工程，导航界面就出来了

![Baidu Navi Demo UI][image-2]

### “算路失败”
从网络上找到了2个地点的经纬度信息，(121.387211，31.088074)和(121.523014，31.277986)，一个作为起始点，一个作为结束点，调用导航函数BNRoutePlanNode，提示”算路失败”的错误。后来发现实参的顺序是经度在前，纬度在后，调换下顺序就导航成功了

### 没有语音
在打开demo时，发现logcat里有句报错

	bdTTSGetAuthorize failed!

这是因为SDK内置百度TTS语音播报功能，需要对app进行授权验证才能够使用，授权方法是 [http://developer.baidu.com/map/index.php?title=android-navsdk/guide/voice][2]

### 离线地图
在百度地图SDK中提供的Demo中，有离线下载城市地图资源包的示例，使用默认路径，看看BaseMapDemo.java里的onCreate函数

	@Override
	    public void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        Intent intent = getIntent();
	        if (intent.hasExtra("x") && intent.hasExtra("y")) {
	            // 当用intent参数时，设置中心点为指定点
	            Bundle b = intent.getExtras();
	            LatLng p = new LatLng(b.getDouble("y"), b.getDouble("x"));
	            mMapView = new MapView(this,
	                    new BaiduMapOptions().mapStatus(new MapStatus.Builder()
	                            .target(p).build()));
	        } else {
	            mMapView = new MapView(this, new BaiduMapOptions());
	        }
	        setContentView(mMapView);
	        mBaiduMap = mMapView.getMap();
	    }

当前一个activity没有传值(key值分别是x和y，对应经纬度信息)过来时，会默认显示北京的地图。知道了这点后，可以通过传值来更改默认的地图显示，如下示例

	Bundle bundle = new Bundle();
	bundle.putDouble("y",31.088074);
	bundle.putDouble("x",121.387211);
	intent.putExtras(bundle);
	startActivity(intent);

[1]:	http://lbsyun.baidu.com/sdk/download
[2]:	http://developer.baidu.com/map/index.php?title=android-navsdk/guide/voice

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/baiduNavi/baiduNavi_01.png
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/baiduNavi/baiduNavi_02.png
