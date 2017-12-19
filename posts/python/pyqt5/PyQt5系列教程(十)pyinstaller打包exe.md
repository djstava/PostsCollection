### 软硬件环境

* Windows 10
* PyQt5 
* Python3.4
* pyinstaller

### 前言

前文[PyQt5系列教程(三)用py2exe进行程序打包](http://xugaoxiang.com/blog/index.php/archives/28/)介绍了如何使用py2exe来给python3工程打包，本文介绍另一个打包工具pyinstaller，个人感觉比py2exe更加简单、方便。

### pyinstaller安装

命令行中执行

```
pip3 install pyinstaller
```

### 打包exe

工程还是以之前的为例，下载地址是[http://download.csdn.net/detail/djstavav/9351205](http://download.csdn.net/detail/djstavav/9351205 "firstPyQt5")，解压进入工程目录，命令行执行打包命令

```
pyinstaller -F --distpath release main.py
```

![pyinstaller_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyinstaller_01.png)

![pyinstaller_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyinstaller_02.png)

![pyinstaller_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyinstaller_03.png)



pyinstaller的打包参数很多，需要的话通过help来查看

```
pyinstaller -h
```

###  参考资料

1、<http://www.pyinstaller.org/>