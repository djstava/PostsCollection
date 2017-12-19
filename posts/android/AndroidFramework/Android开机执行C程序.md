### 软硬件环境

* OTT BOx with android 5.1.1

### 前言

之前的[Android开机执行shell脚本](http://www.xugaoxiang.com/blog/index.php/archives/96/)介绍了如何在android系统开机时执行特定的shell脚本文件，不过在某些情况下不适合用shell实现，又不好做成jni动态库由app调用，这时用C语言程序来实现就有可能帮上你，本文就以创建文件夹为例，来实现android开机启动C可执行程序。

### 实现步骤

##### 在Android源码中创建文件夹

```
cd android_src/external
mkdir djstava
```

##### 创建C源文件及Android.mk

首先是写源码文件test.c

```
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int ret = mkdir("/system/djstava");//请确保你的用户有对相应目录的权限
    if(ret == -1)
    {
        printf("mkdir failed!\n");
        return -1;
    }
    return 0;
}
```

这里如果你的system分区只读，可以换成/data/djstava作为测试用

接下来就是Android.mk文件

```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := eng 
LOCAL_MODULE := djstava 
LOCAL_SRC_FILES := $(call all-subdir-c-files)
include $(BUILD_EXECUTABLE)
```

##### 修改Android开机脚本文件init.${platform}.rc文件，增加

```
service djstava_test /system/bin/djstava
	class main
	user root
	group root
	oneshot
```

##### 重新编译源码并打包

```
cd android_src
make
```

##### 刷机并测试

看看文件夹/system/djstava(或者/data/djstava)是否已经被创建好了。

### 关于作者

Website: <http://www.xugaoxiang.com>
Github:   <https://github.com/djstava>
Email:     djstava@gmail.com