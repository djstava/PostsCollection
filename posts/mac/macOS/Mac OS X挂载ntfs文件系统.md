### 软硬件环境
* Macbook Pro MGX 72
* OS X EI Capitan

### 前言
在使用Mac系统时，经常会碰到连接上移动硬盘，可以看到盘里的东西，可是却无法写盘的情况，报Read-only file system错误，这时大家可能最容易想到的方法就是去安装相应的软件，像NTFS for mac等等，不过一般情况下都会碰到license的问题。今天介绍的这种方法是在命令行下进行操作的，如果你既不想付费购买软件，又不想使用盗版，可以试试这种方法。其实那些App也只是在命令行的基础上增加了一层UI，使用起来更加方便和美观，原理其实都是一样的。

### 查看设备号

	$ mount


![mac_ntfs_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_ntfs/mac_ntfs_01.png)

### 卸载设备
由于在Mac上连接移动硬盘时，系统都会自动挂载，所以需要先从默认的挂载点卸载

	$ sudo umount /dev/disk2s1

### 重新挂载
创建一个空目录，再将设备挂载到这个目录，然后就可以随意操作移动硬盘了

	$ mkdir mnt
	$ sudo mount_ntfs -o rw,nobrowse /dev/disk2s1 mnt
