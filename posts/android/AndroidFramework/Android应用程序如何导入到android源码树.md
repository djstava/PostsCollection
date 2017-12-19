### 软硬件环境

* OTT BOx with android 5.1.1

### 前言

在做android开发时，有时需要事先将应用程序烧写到板子上去而不必让用户在板子出厂后自行安装。将应用导入到android源码树可以分为两种情况：应用的apk文件和工程源码，下面将一一介绍。

### APK文件形式导入

这种方式最简单，app源码与android源码分开管理，互不相干。App发布后，再放进android源码库中编译打包。主要有一下几个步骤

##### 在应用程序所在目录创建文件夹

一般是packages/apps/这个目录或者厂家自己存放apps的位置

```
mkdir Hello
```

##### 将Hello.apk拷贝到刚才所创建的文件夹下

```
copy Hello.apk Hello
```

##### 在apk同级创建Android.mk文件

```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
	
LOCAL_MODULE := Hello.apk
LOCAL_MODULE_TAGS := eng
LOCAL_MODULE_CLASS := EXECUTABLE
LOCAL_MODULE_PATH := $(TARGET_OUT_APPS)
LOCAL_SRC_FILES := $(LOCAL_MODULE)
```

##### 到android根目录进行编译

```
make
```

### 应用源码形式导入

##### 在应用程序所在目录创建文件夹

一般是packages/apps/这个目录或者厂家自己存放apps的位置

```
mkdir Hello
```

##### 将工程源码拷贝到Hello文件夹下

```
copy -rf Hello/* Hello/
```

 ##### 新建Android.mk文件

```
LOCAL_PATH:= $(call my-dir) 
include $(CLEAR_VARS)

LOCAL_SRC_FILES := $(call all-java-files-under, src)
LOCAL_PACKAGE_NAME := Hello
LOCAL_MODULE_TAGS := eng 
	
include $(BUILD_PACKAGE)
```

##### 到android根目录进行编译

```
make
```

##### 如果java中调用了c库，则工程中会有个jni目录

![source_tree_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/framework/source_tree_01.png)

这里的Android.mk是用来编译C文件产生lib*.so的，如

```
LOCAL_PATH := $(call my-dir) 
include $(CLEAR_VARS) 

# java中System.loadLibray()使用 
LOCAL_MODULE := SerialPort
LOCAL_SRC_FILES := SerialPort.c 	
LOCAL_LDLIBS := -llog 
include $(BUILD_SHARED_LIBRARY)
```

##### 此时工程的Android.mk可如下

```
LOCAL_PATH:= $(call my-dir) 
include $(CLEAR_VARS) 
LOCAL_MODULE_TAGS := eng 
LOCAL_SRC_FILES := $(call all-java-files-under, src) 
LOCAL_PACKAGE_NAME := testApp 
LOCAL_CERTIFICATE := platform 
LOCAL_JNI_SHARED_LIBRARIES := libserial_port 
LOCAL_PROGUARD_ENABLED := disabled 
include $(BUILD_PACKAGE) 
include $(call all-makefiles-under,$(LOCAL_PATH))
```

##### 重新编译

```
make
```

### 关于作者

Website: <http://www.xugaoxiang.com>
Github:   <https://github.com/djstava>
Email:     djstava@gmail.com