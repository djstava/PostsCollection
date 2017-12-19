### 软硬件环境
* Android studio 2.1.3
* 坚果手机 5.1.1

### 前言
ViewPager是android中进行View切换的一个控件。它是一个使用率非常高的控件，像app初次使用时的引导页面。本文将来学习一下这个控件以及相关的一些内容。

### ViewPager的使用
要使用ViewPager，一般需要进行如下步骤

1. 主布局中加入ViewPager
2. 确定切换子View的布局
3. 重写PagerAdapter的几个方法
4. 给ViewPager设置adapter

#### 主布局layout_main.xml
MainActivity的布局文件，加入ViewPager控件
    
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.xugaoxiang.viewpagerdemo.MainActivity">
    
        <android.support.v4.view.ViewPager
            android:id="@+id/viewPager"
            android:layout_gravity="center"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        </android.support.v4.view.ViewPager>
    </LinearLayout>

#### layout_tab*.xml
子View的布局文件，只包含一个ImageView。我这里设置了5个切换的View，准备了5张美女图片，其它几个子View的布局跟这个差不多，只有图片不一样，这里就不再写了，自己补全吧。

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
    
        <ImageView
            android:src="@drawable/beauty1"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>
    
#### MainActivity.java文件

    package com.xugaoxiang.viewpagerdemo;
    
    import android.support.v4.view.PagerAdapter;
    import android.support.v4.view.ViewPager;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    
    import java.util.ArrayList;
    import java.util.List;
    
    public class MainActivity extends AppCompatActivity {
        private ViewPager viewPager;
        private List<View> views;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            viewPager = (ViewPager) findViewById(R.id.viewPager);
            LayoutInflater layoutInflater = getLayoutInflater();
    
            views = new ArrayList<View>();
            views.add(layoutInflater.inflate(R.layout.layout_tab1,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab2,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab3,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab4,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab5,null));
    
            PagerAdapter pagerAdapter = new PagerAdapter() {
                @Override
                public int getCount() {
                    return views.size();
                }
    
                @Override
                public boolean isViewFromObject(View view, Object object) {
                    return view == object;
                }
    
                @Override
                public void destroyItem(ViewGroup container, int position, Object object) {
    //                super.destroyItem(container, position, object);
                    container.removeView(views.get(position));
                }
    
                @Override
                public Object instantiateItem(ViewGroup container, int position) {
    //                return super.instantiateItem(container, position);
                    container.addView(views.get(position));
                    return views.get(position);
                }
            };
    
            viewPager.setAdapter(pagerAdapter);
        }
    }

初始化一个List用来存放需要切换的view，然后实例化PagerAdapter并重写getCount()、isViewFromObject()、destroyItem()和instantiateItem()这4个方法。运行下这个工程

[viewpager_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/viewpager/viewpager_01.gif)

### PagerAdapter中需要重写的几个函数
现在回头再看看PagerAdapter需要重写的4个方法

* getCount 返回当前有效子view的个数
* destroyItem 将制定的view从container中移除
* instantiateItem 将视图添加到container中,并将当前视图作为key进行返回
* isViewFromObject 判断instantiateItem方法返回来的key与视图是不是同一个

注意到上面方法解释中谈到了一个新的概念,key。简单来讲,key是视图的标识,根据key就可以找到其对应的view。在我们重写instantiateItem这个方法时,我们将View作为key返回,当然你也可以将position作为key进行返回。这样的话,对应地在重写isViewFromObject方法时,应该先根据position找到view再和参数1的View尽心比较,一致返回ture,否则返回false。

### PagerTitleStrip和PagerTabStrip
PagerTitleStrip和PagerTabStrip都是用来实现标题栏的,它们的写法完全一样,区别在于

1. PagerTabStrip方式的标题栏下有根下划线
2. PagerTabStrip方式的标题栏响应点击事件,可以切换到对应的view

layout_mail.xml布局文件

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.xugaoxiang.viewpagerdemo.MainActivity">
    
        <android.support.v4.view.ViewPager
            android:id="@+id/viewPager"
            android:layout_gravity="center"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
    
            <android.support.v4.view.PagerTitleStrip
                android:id="@+id/pagerTitle"
                android:layout_gravity="top"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content">
            </android.support.v4.view.PagerTitleStrip>
    
        </android.support.v4.view.ViewPager>
    </LinearLayout>

属性android:layout_gravity="top"指定了标题栏设置在了顶部,当然你也可以将它设置在底部。

MainActivity.java中实例化PagerAdapter时,需要再重写一个方法,getPageTitle

    package com.xugaoxiang.viewpagerdemo;
    
    import android.support.v4.view.PagerAdapter;
    import android.support.v4.view.ViewPager;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    
    import java.util.ArrayList;
    import java.util.List;
    
    public class MainActivity extends AppCompatActivity {
        private ViewPager viewPager;
        private List<View> views;
        private List<String> titleList;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            viewPager = (ViewPager) findViewById(R.id.viewPager);
            LayoutInflater layoutInflater = getLayoutInflater();
    
            views = new ArrayList<View>();
            views.add(layoutInflater.inflate(R.layout.layout_tab1,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab2,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab3,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab4,null));
            views.add(layoutInflater.inflate(R.layout.layout_tab5,null));
    
            titleList = new ArrayList<String>();
            titleList.add("第一页Tab");
            titleList.add("第二页Tab");
            titleList.add("第三页Tab");
            titleList.add("第四页Tab");
            titleList.add("第五页Tab");
    
            PagerAdapter pagerAdapter = new PagerAdapter() {
                @Override
                public int getCount() {
                    return views.size();
                }
    
                @Override
                public boolean isViewFromObject(View view, Object object) {
                    return view == object;
                }
    
                @Override
                public void destroyItem(ViewGroup container, int position, Object object) {
    //                super.destroyItem(container, position, object);
                    container.removeView(views.get(position));
                }
    
                @Override
                public Object instantiateItem(ViewGroup container, int position) {
    //                return super.instantiateItem(container, position);
                    container.addView(views.get(position));
                    return views.get(position);
                }
    
                @Override
                public CharSequence getPageTitle(int position) {
    //                return super.getPageTitle(position);
                    return titleList.get(position);
                }
            };
    
            viewPager.setAdapter(pagerAdapter);
        }
    }

PagerTitleStrip的效果

[viewpager_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/viewpager/viewpager_02.gif)


PagerTabStrip的效果

[viewpager_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/viewpager/viewpager_03.gif)

### 循环滑动
前面的ViewPager当滑动到第一个的时候,再想往左滑就滑不动了;当滑动到最后一个的时候,再想往右滑也滑不懂了。如果想实现上述效果,需要重写PagerAdapter的几个方法。

            PagerAdapter pagerAdapter = new PagerAdapter() {
            
            
                @Override
                public int getCount() {
                    return Integer.MAX_VALUE;
                }
    
                @Override
                public boolean isViewFromObject(View view, Object object) {
                    return view == object;
                }
    
                @Override
                public void destroyItem(ViewGroup container, int position, Object object) {
    //                super.destroyItem(container, position, object);
                    container.removeView(views.get(position % views.size()));
                }
    
                @Override
                public Object instantiateItem(ViewGroup container, int position) {
    //                return super.instantiateItem(container, position);
                    container.addView(views.get(position % views.size()));
                    return views.get(position % views.size());
                }
    
                @Override
                public CharSequence getPageTitle(int position) {
    //                return super.getPageTitle(position);
                    return titleList.get(position % views.size());
                }
            };

[viewpager_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/viewpager/viewpager_04.gif)


### 本文源码下载
* http://download.csdn.net/detail/djstavav/9614782
