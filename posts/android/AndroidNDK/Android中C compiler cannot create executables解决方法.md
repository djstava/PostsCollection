### 软硬件环境

* android 5.1..1
* ndk r15c

### 错误描述

![vlc_android_compile_error_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/vlc/vlc_android_compile_error_01.png)

![vlc_android_compile_error_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/vlc/vlc_android_compile_error_02.png)



### 解决思路

这个一般是跟ndk相关的错误，某些头文件或者obj文件找不到。可以编写个简单的hello world源文件测试

```
#include <stdio.h>

int main() {
    printf("Hello world.");
    return 0;
}
```

使用ndk中的编译器进行编译，如

```
export $ANDROID_NDK=/home/longjing/tools/Android/android-ndk-r15c
$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-gcc test.c
```

编译果然报错，找不到头文件

```
../test.c:16:19: fatal error: stdio.h: No such file or directory
 #include <stdio.h>
                   ^
compilation terminated.
```

既然是测试文件，那干脆就去掉include语句，再删除printf语句，重新编译看下结果

```
//#include <stdio.h>

int main() {
    //printf("Hello world.");
    return 0;
}
```

这下就告诉你obj文件找不到了

```
/home/longjing/tools/Android/android-ndk-r15c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../arm-linux-androideabi/bin/ld: error: cannot open crtbegin_dynamic.o: No such file or directory
/home/longjing/tools/Android/android-ndk-r15c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../arm-linux-androideabi/bin/ld: error: cannot open crtend_android.o: No such file or directory
/home/longjing/tools/Android/android-ndk-r15c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../arm-linux-androideabi/bin/ld: error: cannot find -lc
/home/longjing/tools/Android/android-ndk-r15c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../arm-linux-androideabi/bin/ld: error: cannot find -ldl
collect2: error: ld returned 1 exit status
```

使用find命令在ndk目录下搜索下crtend_android.o文件

```
cd /home/longjing/tools/Android/android-ndk-r15c
find . -iname crtend_android.o
```

![vlc_android_compile_error_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/vlc/vlc_android_compile_error_03.png)

可以看到，obj文件都是存在的。那么怎么解决呢？既然找不到，那就给你个路径

```
$ANDROID_NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-gcc test.c --sysroot=$ANDROID_NDK/platforms/android-9/arch-arm
```

最后重新编译，错误消失了。
