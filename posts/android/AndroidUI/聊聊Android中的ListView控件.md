### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.2
* 坚果手机

### 前言
ListView是Android系统中使用非常广泛的一种控件,几乎所有的App都会用到它。它以垂直列表的形式来显示所有的列表项内容，简单直观。

### 使用方法
通常情况下，listview都是按如下步骤进行使用的  
1. 通过findViewById初始化listview  
2. 获取数据  
3. 将数据通过setAdapter装配到listview中  
4. 需要的话通过adapter.notifyDataSetChanged刷新数据  

### 常用XML属性

xml属性           |           详细说明
:----------------|:-----------------------------------------------
divider          |  设置列表项的分隔符,@null表示不设置
dividerHeight    |  设置表项分隔符的高度
entries          |  指定列表资源，它是一个数组，在strings.xml中定义
headerDividersEnabled | 如果设置成false，则不在header view之后绘制分隔条
footerDividersEnabled | 如果设置成false，则不在footer view之前绘制分隔条
scrollbars       |  设置滚动条(horizontal、vertical、none)
overScrollMode   |  设置over-scroll模式(always、ifContentScrolls、never)

### 自定义适配器
自定义适配器，其实就是根据自己的实际需要实现相应的适配器。下面以一个实例来说明如何来自定义一个listview的适配器，说白了其实就是自定义每一个具体的item，搭载特定的控件或者控件组合。

##### 新建工程
工程名就叫做ListViewThings，这里的item是一个图片(ImageView)和文本(TextView)的组合。

##### activity\_main.xml
ListView控件在这里声明

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:tools="http://schemas.android.com/tools"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:layout_marginLeft="@dimen/activity_horizontal_margin"
	android:layout_marginRight="@dimen/activity_horizontal_margin"
	android:layout_marginTop="@dimen/activity_vertical_margin"
	android:layout_marginBottom="@dimen/activity_vertical_margin"
	tools:context=".MainActivity">

	<ListView
	    android:id="@+id/listview"
	    android:layout_width="match_parent"
	    android:layout_height="wrap_content"
	    android:dividerHeight="15dp"
	    android:divider="@null"
	    android:scrollbars="none"
	    android:footerDividersEnabled="false"
	    android:headerDividersEnabled="false"></ListView>

</LinearLayout>

##### listview\_item.xml
这个是具体每个item的控件声明，包含一张图片(ImageView)和一段文本(TextView)

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	android:gravity="center"
	android:orientation="vertical">

	<ImageView
	    android:id="@+id/imageview"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content" />

	<TextView
	    android:id="@+id/textview"
	    android:textSize="20sp"
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content" />

	</LinearLayout>

##### MainActivity.java
首先你要获取数据，这些数据会在适配器中用到，这里我们用2个数组，1个表示图片，1个表示文本，然而实际情况可能要复杂得多。然后我们定义一个继承自BaseAdapter的适配器ListViewAapter，它来负责整个数据的装填，特别注意getView方法。在Android Studio中，需要重写父类的方法时，你可以利用其自动匹配的特性来重写某些方法，加速开发。

	package com.djstava.listviewthings;

	import android.content.Context;
	import android.support.v7.app.AppCompatActivity;
	import android.os.Bundle;
	import android.view.LayoutInflater;
	import android.view.Menu;
	import android.view.MenuItem;
	import android.view.View;
	import android.view.ViewGroup;
	import android.widget.BaseAdapter;
	import android.widget.ImageView;
	import android.widget.ListView;
	import android.widget.TextView;

	public class MainActivity extends AppCompatActivity {
	    private static final String TAG = MainActivity.class.getSimpleName();
	    private ListView listView;

	    private int images[] = {R.mipmap.ic_launcher, R.mipmap.ic_launcher, R.mipmap.ic_launcher, R.mipmap.ic_launcher};
	    private int text[] = {R.string.hello_world, R.string.hello_world, R.string.hello_world, R.string.hello_world};

	@Override
	protected void onCreate(Bundle savedInstanceState) {
	    super.onCreate(savedInstanceState);
	    setContentView(R.layout.activity_main);

	    listView = (ListView) findViewById(R.id.listview);
	    ListViewAdapter listViewAdapter = new ListViewAdapter(getApplicationContext());
	    listView.setAdapter(listViewAdapter);
	}

	class ListViewAdapter extends BaseAdapter {
	    private Context context;

	    public ListViewAdapter(Context context) {
	        this.context = context;
	    }

	    @Override
	    public int getCount() {
	        return images.length;
	    }

	    @Override
	    public long getItemId(int position) {
	        return position;
	    }

	    @Override
	    public Object getItem(int position) {
	        return null;
	    }

	    @Override
	    public View getView(int position, View convertView, ViewGroup parent) {
	        View view = convertView;
	        ViewHolder viewHolder;

	        if (view == null) {
	            view = LayoutInflater.from(context).inflate(R.layout.listview_item, parent, false);

	            viewHolder = new ViewHolder();
	            viewHolder.imageView = (ImageView) view.findViewById(R.id.imageview);
	            viewHolder.textView = (TextView) view.findViewById(R.id.textview);

	            viewHolder.imageView.setImageResource(images[position]);
	            viewHolder.textView.setText(text[position]);
	            view.setTag(viewHolder);
	        } else {
	            viewHolder = (ViewHolder) view.getTag();
	        }

	        return view;
	    }
	}

	static class ViewHolder {
	    ImageView imageView;
	    TextView textView;
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


![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/listview/listviewThings_01.png)

### 顶部、底部分隔线
如果不想显示listview的各个item之前的分隔符，可以在xml文件中设置

	android:divider="@null"

或者在java文件中设置

	listview.setDivider(null);

设置完成后，发现listview的顶部和底部都有一根黑线，不同的机器可能会有不同的表现。酷比魔方七寸平板上就有这个现象，而锤子的坚果手机却没有。

如果你也碰到这种情况，不要慌张，你可以在xml文件进行如下设置

	android:footerDividersEnabled="false"
	android:headerDividersEnabled="false"
不幸如我，在进行了上述设置后，顶部和底部的黑线依然存在，真是欲哭无泪，没办法，只能继续寻找解决方法，翻了翻官方的API，看到overScrollMode这个属性，它的默认值是always，意指始终打开回弹效果，而可选值ifContentScrolls则表示如果当前列表显示不了全部的数据时有回弹效果，于是进行设置

	overScrollMode="never"

可恨的黑线终于不见了。

### 性能优化
常见的ListView的优化点在于getView方法重写过程中。在上面的例子中，我们也是这样做的，重用了convertView，减少了内存的消耗。

如果convertView为null的话，就inflate出一个view，然后通过这个view来找到item里面的控件，设置数据，进而呈现给用户。

还有另外一点，每次在getView中，都需要重新调用findViewById来找到控件，这点是完全没有必要的，因为每一个item的控件或控件组合是一样的，因此只要做过一次就够了，后面的可以重复利用。为了解决这个问题，我们定义一个static内部类，静态类只会在第一次加载时会耗费比较长时间，后面加载就很快了，而且也确保了内存中只有一个。

	static class ViewHolder {
	    ImageView imageView;
	    TextView textView;
	}

ViewHolder用来标识view中的控件，方便进行事件处理，这样就不用每次都去findViewById了，从而提升了性能。

### 源码下载

[http://download.csdn.net/detail/djstavav/9126983](http://download.csdn.net/detail/djstavav/9126983)

### 参考资料
1、[http://developer.android.com/intl/zh-cn/reference/android/view/View.html#setOverScrollMode(int)](http://developer.android.com/intl/zh-cn/reference/android/view/View.html#setOverScrollMode(int))
