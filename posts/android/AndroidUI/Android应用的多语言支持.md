### 软硬件环境

* Windows 10
* Android studio 2.3.2
* OTT BOx with android 5.1.1

### 前言

App开发测试完成后就面临着应用上架，除了国内的上架渠道，android阵营上架Google play我觉得是必不可少的。这样的话，中英文支持就成了必须的功能点了。本文就来实现这个功能。

### 设置不同语言的资源文件

如有必要，res文件夹下的像drawable、layout、values等都需要做不同语言的处理，这里仅以values为例

![language_res](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/language/language_res.png)

values-en和values-zh-rCN文件夹中的内容都一样values-en就是values-zh-rCN对应的英文翻译，如strings.xml文件内容如下

![language_string](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/language/language_string.png)

接下来就是如何使用对应资源，非常简答，以Toast为例

```
Toast.makeText(MainActivity.this, R.string.play_error, Toast.LENGTH_LONG).show();
```

### 系统当前的语言环境

这里仅处理中文、英文，如果还有其它语言，需要稍作修改

```
private boolean isZh() {
    Locale locale = getResources().getConfiguration().locale;
    String language = locale.getLanguage();
    if (language.endsWith("zh")) {
        return true;
    } else {
        return false;
    }
}
```

### App内设置语言

```
//获取res资源对象
Resources resources = getResources();    
//获取设置对象
Configuration config = resources.getConfiguration();   
//获取屏幕参数：主要是分辨率，像素等
DisplayMetrics dm = resources.getDisplayMetrics(); 
//设置APP语言设置为英文
config.locale = Locale.ENGLISH;  
//设置完以后要刷新Activity才能及时生效
resources.updateConfiguration(config, dm);
```

### 资源文件夹后缀

给出一个常用的语言文件夹命名列表，方便查询

```
中文（中国）：values-zh-rCN
中文（台湾）：values-zh-rTW
中文（香港）：values-zh-rHK
英语（美国）：values-en-rUS
英语（英国）：values-en-rGB
英文（澳大利亚）：values-en-rAU
英文（加拿大）：values-en-rCA
英文（爱尔兰）：values-en-rIE
英文（印度）：values-en-rIN
英文（新西兰）：values-en-rNZ
英文（新加坡）：values-en-rSG
英文（南非）：values-en-rZA
阿拉伯文（埃及）：values-ar-rEG
阿拉伯文（以色列）：values-ar-rIL
保加利亚文:  values-bg-rBG
加泰罗尼亚文：values-ca-rES
捷克文：values-cs-rCZ
丹麦文：values-da-rDK
德文（奥地利）：values-de-rAT
德文（瑞士）：values-de-rCH
德文（德国）：values-de-rDE
德文（列支敦士登）：values-de-rLI
希腊文：values-el-rGR
西班牙文（西班牙）：values-es-rES
西班牙文（美国）：values-es-rUS
芬兰文（芬兰）：values-fi-rFI
法文（比利时）：values-fr-rBE
法文（加拿大）：values-fr-rCA
法文（瑞士）：values-fr-rCH
法文（法国）：values-fr-rFR
希伯来文：values-iw-rIL
印地文：values-hi-rIN
克罗里亚文：values-hr-rHR
匈牙利文：values-hu-rHU
印度尼西亚文：values-in-rID
意大利文（瑞士）：values-it-rCH
意大利文（意大利）：values-it-rIT
日文：values-ja-rJP
韩文：values-ko-rKR
立陶宛文：valueslt-rLT
拉脱维亚文：values-lv-rLV
挪威博克马尔文：values-nb-rNO
荷兰文(比利时)：values-nl-BE
荷兰文（荷兰）：values-nl-rNL
波兰文：values-pl-rPL
葡萄牙文（巴西）：values-pt-rBR
葡萄牙文（葡萄牙）：values-pt-rPT
罗马尼亚文：values-ro-rRO
俄文：values-ru-rRU
斯洛伐克文：values-sk-rSK
斯洛文尼亚文：values-sl-rSI
塞尔维亚文：values-sr-rRS
瑞典文：values-sv-rSE
泰文：values-th-rTH
塔加洛语：values-tl-rPH
土耳其文：values--r-rTR
乌克兰文：values-uk-rUA
越南文：values-vi-rVN
```
### App中更改系统语言

这个其实系统的Settings已经实现了，查看源码就可以看到它使用的是LocalePicker.updateLocale(locale)这个函数，但是这个类是在com.android.internal.app包下，上层的app脱离了android源码根本无法调用。那在只有SDK的环境下，如何实现呢？对，java反射机制。

```
private void updateLocale(Locale locale) throws Exception {
        Method method[];
        Class<?> localeClass;
        localeClass = Class.forName("com.android.internal.app.LocalePicker");
        method = localeClass.getDeclaredMethods();
        method[method.length - 1].invoke(localeClass, locale);
}
```

别忘了在AndroidManifest.xml加上权限

```
<uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
```

在我的硬件上，发现，即使加入了android.permission.CHANGE_CONFIGURATION，也会报Permission Denial的错误，怎么办呢？方法还是有的,adb shell到设备上，执行

```
pm grant com.xugaoxiang.localeTest android.permission.CHANGE_CONFIGURATION
```

这里com.xugaoxiang.localeTest是你的包名，设置完后，就能够在App中更改系统的语言了。