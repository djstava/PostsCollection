### 软硬件环境

* ubuntu 16.04 64bit

### 前言

机器用久了，慢慢地，硬盘空间就不够用了，然后就去买了新硬盘，如何在不动现有系统的情况下无缝的安装新硬盘，达到快速扩容的目的，本文就来解决这个问题。这里有个前提，就是ubuntu系统安装时需要创建LVM。

### LVM是什么

LVM，即Logical Volume Manager，逻辑卷管理。LVM可以方便地将一个或多个硬盘在逻辑上集合起来，相当于一个大硬盘。

### LVM实战

电脑中原来有一块硬盘，2.7TB，手头上还有另外3块新的SATA硬盘，现在需要将这余下的3块新硬盘添加到电脑中，达到扩容的目的。

![lvm_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_01.png)

把3块新硬盘接到电脑上，查看磁盘情况

##### ![lvm_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_02.png)

处理第一块硬盘/dev/sdb，进入到fdisk命令后，n为创建新分区，回车选择默认值，t为选择类型，一定要选8e，意即LVM

![lvm_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_03.png)



![lvm_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_04.png)

最后执行w命令进行保存

![lvm_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_05.png)

接下来创建PV，Physical Volume，物理卷是指硬盘分区或从逻辑上与磁盘分区具有同样功能的设备(如RAID)，是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，多了一些与LVM相关的管理参数

![lvm_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_06.png)

查看VG，Volume Group：LVM卷组类似于非LVM系统中的物理硬盘，其由物理卷组成。可以在卷组上创建一个或多个“LVM分区”（逻辑卷），LVM卷组由一个或多个物理卷组成

![lvm_07](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_07.png)

这里的longjingdata-vg就是vgdisplay输出的VG Name

![lvm_08](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_08.png)/

```
lvextend -L +2T /dev/longjingdata-vg/root
resize2fs /dev/longjingdata-vg/root
```

这样一块新硬盘就添加成功了，其余的2块，依照上面的操作依样画葫芦就行了，这里不再赘述。

最后查看下结果，满足预期。

![lvm_09](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/lvm/lvm_09.png)