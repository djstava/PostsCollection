### 软硬件环境
* ubuntu 14.04
* Android studio2.1.2
* Android 5.1.1

### 前言
一般的Android应用程序,在安装后,程序只能访问/data/data/${程序包名}下的数据，如常见的SharedPreferences、database等。
而如果想让app访问其它地方的资源时，就必须要获取更高的权限，像system或者root。本文完成system权限的获取，实际上一般的应用有system的权限基本上也够了。

### 修改apk内的AndroidManifest.xml
在app中的AndroidManifest.xml文件中的manifest标签中加入
    
    android:sharedUserId="android.uid.system" 

### APP工程生成apk文件
android studio或者eclipse自动就帮你生成好了

### 给apk文件进行签名
这里分两种情况,一种是厂商,有android系统源码,既可以把app源码导入到android源码中,也可以单独完成签名。第二种是你仅仅是做应用的,拿不到厂商的source code,而且
这种情况也相当普遍,怎么办?别着急,我们今天就来解决这个问题。

#### 签名需要的几个文件
这个你必须找厂商要,signapk.jar、platform.x509.pem、platform.pk8,如果你有source code,signapk.jar存放在prebuilts/sdk/tools/,platform.*
两个key文件存放在build/target/product/security/下

#### 签名具体命令

    java -jar signapk.jar platform.x509.pem platform.pk8 unsigned.apk signed.apk

### 制作新的image文件
如果你的应用需要预置到android系统中,将signed.apk导入到android源码目录中(一般是out/target/product/${hardware}/system/app/下)，编译生成新的
system.img，再烧录到目标板中。如果不需要预置,那就直接adb install好了。

### 测试
待系统启动后adb shell到板子上,ls -l查看app安装目录(/data/data/${程序包名})的权限，看看是不是变成system:system了。