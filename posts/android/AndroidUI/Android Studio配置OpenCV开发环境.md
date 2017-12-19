### 软硬件环境
* Macbook Pro MGX 72
* Android studio 1.0.2
* OpenCV 2.4.10
* 红米Note增强版

### 下载OpenCV
本文中使用的是2.4.10版本，从<http://opencv.org/downloads.html>下载zip包，解压备用

### 新建android工程
这个不用说，一步一步跟着向导做就行了，我这里取名HandDetection

### 导入OpenCV
Android Studio --> File --> Import Module，选择解压后的OpenCV-2.4.10-android-sdk/sdk/java
导入完成以后，会在工程HandDetection根目录下生成目录openCVLibrary2410

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/opencv/android_openCV_01.png)

### 修改openCVLibrary2410下的build.gradle
修改compileSdkVersion、buildToolsVersion、minSdkVersion、targetSdkVersion，它们的值与工程下的build.gradle中的相应字段一致，否则系统会报错

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/opencv/android_openCV_02.png)

### 增加模块依赖
Android Studio --> File -->Project Structure -->Dependencies

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/opencv/android_openCV_03.png)

点击下方的 "+" 图标，将openCVLibrary2410勾选

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/opencv/android_openCV_04.png)

### 创建jniLibs目录
将解压后的OpenCV-2.4.10-android-sdk/sdk/native/libs目录拷贝到工程下的app/src/main目录下，并重命名为jniLibs

### 测试
在MainActivity里import个OpenCV包看看是否可以自动导入

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/opencv/android_openCV_05.png)

### 参考文献

1 <http://opencv.org>    
2 <http://stackoverflow.com/questions/27406303/opencv-in-android-studio>
