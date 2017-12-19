### 软硬件环境

* Windows 10
* Python 3.5.2
* PyQt5
* pyinstaller 

### 前言

前面一篇博文[PyQt5系列教程(十)pyinstaller打包exe](http://www.xugaoxiang.com/blog/index.php/archives/77/)已经介绍过如何使用pyinstaller打包exe，最近在打包包含subprocess.Popen时发现，加上参数—noconsole时产生的exe文件在运行的时候，进程并没有正确运行。经过一番google，问题得以解决，现将解决方法记录一下，形成此文。

### subprocess的使用

我这里需要利用subprocess.Popen创建一个进程去执行一个命令行操作，

```
mProcess = subprocess.Popen(cmd,stdin=subprocess.DEVNULL, stdout=subprocess.PIPE, stderr=subprocess.STDOUT,universal_newlines=True)

```

pyinstaller打包操作命令如下

```
pyinstaller -F --noconsole --clean --distpath release bin\\troutling
```

打包后生成的exe，可以运行，不过查看进程并没有如预期正确地工作。

### 解决方法

在创建进程时，加上startupinfo参数，如下

```
si = subprocess.STARTUPINFO()
si.dwFlags |= subprocess.STARTF_USESHOWWINDOW
mProcess = subprocess.Popen(cmd,stdin=subprocess.DEVNULL, stdout=subprocess.PIPE, stderr=subprocess.STDOUT,universal_newlines=True,startupinfo=si)
```

问题完美解决，具体可以参考下参考资料

### 参考资料

1 https://github.com/pyinstaller/pyinstaller/wiki/Recipe-subprocess

2 https://github.com/djstava/troutling