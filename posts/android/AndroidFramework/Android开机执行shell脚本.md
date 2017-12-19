### 软硬件环境

* OTT BOx with android 5.1.1

### 前言

在应用程序启动之前，往往需要在Android启动过程中去对系统做一些设置，如修改某些文件夹的属主和文件的权限等。本文实现在init.*.rc中调用shell脚本，而所有对系统资源的修改和配置都在此shell脚本中实现，最后讲一下关闭android selinux的三种方法。

### Shell脚本

这里以创建蓝牙设备结点为例，shell文件名为makebtusb0.sh，在脚本中创建结点，然后修改结点属性，具体如下

```
#! /system/bin/sh

mknod /dev/btusb0 c 180 194
chmod 777 /dev/btusb0
```

### 修改init.${platform}.rc文件

这个文件一般是放在device目录下，如果没有的话，也可以直接加到init.rc中

```
service makenode /system/etc/makebtusb0.sh
    class main
    user root
    group root 
    oneshot
```

上述命令中，包含一些android init language的知识点，如果想了解的话，请见参考资料1，写得非常详细。

### 关闭Android selinux的几种方法

刚好在集成蓝牙驱动中碰到了这个问题，就在这里一并记录下，希望其他朋友少走弯路

##### 方法一

```
setenforce 0
```

##### 方法二

```
echo 0 > /sys/fs/selinux/enforce
```

##### 方法三

编辑BoardConfig.mk

```
BOARD_KERNEL_CMDLINE := console=ttyS0,115200n8 androidboot.hardware=zx2000 androidboot.serialno=01234567890123456789 androidboot.selinux=permissive
```

方法一和方法二适合在终端中执行，当然放在这篇文章中，也可以利用shell脚本实现。方法三在源码中设定，编译打包镜像后就不用再做设置，推荐用这种方法。

### 关于作者

Website: <http://www.xugaoxiang.com>
Github:   <https://github.com/djstava>
Email:     djstava@gmail.com

### 参考资料

<https://www.kancloud.cn/digest/shichang-framework/131973>

