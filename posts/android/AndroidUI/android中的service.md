###软硬件环境
* ubuntu kylin 14.04
* 红米note增强版
* Android studio 0.8.6

###概述
Service就是Android系统中的服务。Service不像Activity，它无法直接和用户进行交互，必须由用户或者程序显式地启动。另一方面，Service的优先级比较高，这就使得当系统资源紧缺时,Service被销毁的机率非常低。一般来说，类似在当前系统交互中想继续处理其它数据类似的场景就会使用到Service，如想一边看电子书一边听音乐。

###Service的生命周期
相比Activity的生命周期，Service的会简单很多

	void onCreate();
	int onStartCommand(Intent intent,int flags,int startId);//替换了原来的onStart(Intent intent);
	void onDestroy();

Service从onCreate开始，到onDestroy结束，onStartCommand启动Service。如果Service已经启动，而我们再次去启动时，不会执行onCreate方法，而是直接去执行onStartCommand方法。如图所示:

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/service/service_01.png)

上图列出了2种启动Service的方式，一种是通过startService，另一种是通过bindService。

###Activity和Service通信实例
目标效果图如下    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/service/service_02.png)

新建工程AndroidServiceDemo，Activity名称为AndroidServiceDemoActivity，然后在源码目录创建一个service文件AndroidServiceDemo.java，即Android studio中的右键-->New-->Service-->Service。

布局文件activity_android_service_demo.xml

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:tools="http://schemas.android.com/tools"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:paddingLeft="@dimen/activity_horizontal_margin"
	android:paddingRight="@dimen/activity_horizontal_margin"
	android:paddingTop="@dimen/activity_vertical_margin"
	android:paddingBottom="@dimen/activity_vertical_margin"
	tools:context=".AndroidServiceDemoActivity">
	
	<Button
	    android:id="@+id/startButton"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:layout_centerHorizontal="true"
	    android:text="Start Service" />
	
	<Button
	    android:id="@+id/stopButton"
	    android:layout_below="@+id/startButton"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:layout_centerHorizontal="true"
	    android:text="Stop Service" />
	
	<TextView
	    android:id="@+id/textFake"
	    android:layout_below="@+id/stopButton"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content" />
	
	<Button
	    android:id="@+id/bindButton"
	    android:layout_below="@+id/textFake"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:layout_centerHorizontal="true"
	    android:text="Bind Service" />
	
	<Button
	    android:id="@+id/unbindButton"
	    android:layout_below="@+id/bindButton"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:layout_centerHorizontal="true"
	    android:text="Unbind Service" />
	
	</RelativeLayout>

AndroidManifest.xml文件由android studio自动生成，不需要改动，注意下service标签就可以了,每个service对应一组标签。

	<service
	    android:name=".AndroidServiceDemo"
	    android:enabled="true"
	    android:exported="true" >
	</service>

接下来看看创建的service的代码AndroidServiceDemo.java

	package com.macernow.djstava.androidservicedemo;

	import android.app.Service;
	import android.content.Intent;
	import android.os.IBinder;
	import android.util.Log;
	import android.widget.Toast;
	
	public class AndroidServiceDemo extends Service {
	
		private static final String TAG = "AndroidServiceDemo";
	
		public AndroidServiceDemo() {
		}
	
		@Override
		public IBinder onBind(Intent intent){
			Log.e(TAG,"onBind... ...");
	
	    	return null;
		}
	
		@Override
		public void onCreate(){
			Log.e(TAG,"onCreate... ...");
			super.onCreate();
		}
	
		@Override
		public void onDestroy(){
			Log.e(TAG,"onDestroy... ...");
			super.onDestroy();
		}
	
		@Override
		public int onStartCommand(Intent intent,int flags,int startId){
			Log.e(TAG,"onStartCommand... ...");
			return super.onStartCommand(intent,flags,startId);
		}
	}

onBind方法需要返回一个IBinder对象，简单起见，这里直接返回null了。

最后看看Activity的相关代码AndroidServiceDemoActivity.java

	package com.macernow.djstava.androidservicedemo;

	import android.app.Activity;
	import android.content.ComponentName;
	import android.content.Intent;
	import android.content.ServiceConnection;
	import android.os.Bundle;
	import android.os.IBinder;
	import android.util.Log;
	import android.view.Menu;
	import android.view.MenuItem;
	import android.view.View;
	import android.widget.Button;


	public class AndroidServiceDemoActivity extends Activity {
		private static final String TAG = "AndroidServiceDemoActivity";
		private static Button mStartButton,mStopButton,mBindButton,mUnbindButton;

    	@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_android_service_demo);
    
        	mStartButton = (Button) findViewById(R.id.startButton);
        	mStopButton = (Button) findViewById(R.id.stopButton);
        	mBindButton = (Button) findViewById(R.id.bindButton);
        	mUnbindButton = (Button) findViewById(R.id.unbindButton);
    
        	mBindButton.setOnClickListener(new View.OnClickListener() {
            	@Override
            	public void onClick(View v) {
                	Log.e(TAG, "mBindButton... ...");
                	bindService(new Intent(AndroidServiceDemoActivity.this,AndroidServiceDemo.class), mConnection, BIND_AUTO_CREATE);
            	}
        	});
    
        	mStartButton.setOnClickListener(new View.OnClickListener() {
            	@Override
            	public void onClick(View v) {
                	Log.e(TAG, "mStartButton... ...");
                	startService(new Intent(AndroidServiceDemoActivity.this,AndroidServiceDemo.class));
            	}
        	});
    
        	mStopButton.setOnClickListener(new View.OnClickListener() {
            	@Override
            	public void onClick(View v) {
                	Log.e(TAG, "mStopButton... ...");
                	stopService(new Intent(AndroidServiceDemoActivity.this,AndroidServiceDemo.class));
            	}
        	});
    
        	mUnbindButton.setOnClickListener(new View.OnClickListener() {
            	@Override
            	public void onClick(View v) {
                	Log.e(TAG, "mUnbindButton... ...");
                	unbindService(mConnection);
            	}
        	});
    	}
    
    	ServiceConnection mConnection = new ServiceConnection() {
        	@Override
        	public void onServiceConnected(ComponentName name, IBinder service) {
            	Log.e(TAG,"onServiceConnected... ...");
        	}
    
        	@Override
        	public void onServiceDisconnected(ComponentName name) {
            	Log.e(TAG,"onServiceDisconnected... ...");
        	}
    	};
    
    	@Override
    	protected void onDestroy(){
        	Log.e(TAG,"onDestroy... ...");
        	unbindService(mConnection);
    	}
    
    	@Override
    	public boolean onCreateOptionsMenu(Menu menu) {
        	// Inflate the menu; this adds items to the action bar if it is present.
        	getMenuInflater().inflate(R.menu.android_service_demo, menu);
        	return true;
    	}
    
    	@Override
    	public boolean onOptionsItemSelected(MenuItem item) {
        	// Handle action bar item clicks here. The action bar will
        	// automatically handle clicks on the Home/Up button, so long
        	// as you specify a parent activity in AndroidManifest.xml.
        	int id = item.getItemId();
        	if (id == R.id.action_settings) {
            	return true;
        	}
        	return super.onOptionsItemSelected(item);
    	}
    }

上面代码中出现了一个ServiceConnection类，并实现了onServiceConnected和onServiceDisconnected方法。一般来讲，bind service后的处理操作放在onServiceConnected方法中进行。而bindService和unbindService方法都是对ServiceConnection对象进行操作。

依次点击应用界面上的4个按钮，logcat中的打印如下

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/service/service_03.png)

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/service/service_04.png)

可以看到stopService和unbindService都是调用的onDestroy方法。


###参考资料
1、<http://developer.android.com/reference/android/app/Service.html>    
2、<http://developer.android.com/guide/topics/manifest/service-element.html>    
