### 软硬件环境
* Macbook pro MGX 72
* 红米Note增强版
* Android studio 1.0.2

### 概述
Android的javascript扩展，即Android提供了javascript网页脚本调用java类方法的机制。方法其实很简单，只要调用addJavascriptInterface方法即可映射一个java对象到javascript对象。

### 示例
* Android代码

		package com.macernow.djstava;

		import android.support.v7.app.ActionBarActivity;
		import android.os.Bundle;
		import android.util.Log;
		import android.view.Menu;
		import android.view.MenuItem;
		import android.webkit.JavascriptInterface;
		import android.webkit.WebView;


		public class MainActivity extends ActionBarActivity {

	    private static final String TAG = "MainActivity";
	    private WebView webView;

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);

	        webView = (WebView)findViewById(R.id.webView);

	        webView.getSettings().setJavaScriptEnabled(true);

	        webView.addJavascriptInterface(this,"djstava");
	        webView.loadUrl("http://192.168.31.109/demo.html");

    	}

	    @JavascriptInterface
	    public void jsextent() {
	        Log.e(TAG,"========== js extension success.");
	    }

	    @Override
	    public boolean onCreateOptionsMenu(Menu menu) {
	        // Inflate the menu; this adds items to the action bar if it is present.
	        getMenuInflater().inflate(R.menu.menu_main, menu);
	        return true;
	    }

	    @Override
	    public boolean onOptionsItemSelected(MenuItem item) {
	        // Handle action bar item clicks here. The action bar will
	        // automatically handle clicks on the Home/Up button, so long
	        // as you specify a parent activity in AndroidManifest.xml.
	        int id = item.getItemId();

	        //noinspection SimplifiableIfStatement
	        if (id == R.id.action_settings) {
	            return true;
	        }

	        return super.onOptionsItemSelected(item);
	    	}
		}

WebView一定要使能javascript。在Android 4.2之后，抛出给javascript的java方法必须加上@JavascriptInterface。

* 布局文件

		<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
	    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
	    android:paddingRight="@dimen/activity_horizontal_margin"
	    android:paddingTop="@dimen/activity_vertical_margin"
	    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

	    <WebView
	        android:id="@+id/webView"
	        android:layout_width="fill_parent"
	        android:layout_height="fill_parent">

	    </WebView>

		</RelativeLayout>

* 测试页面

在javascript脚本中调用java方法

	window.djstava.jsextent()

### 工程下载
<http://download.csdn.net/detail/djstava/8318657>
