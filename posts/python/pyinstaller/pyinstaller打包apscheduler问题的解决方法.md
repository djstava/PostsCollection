### 软硬件环境

* ubuntu 16.04 64bit

- python3.5
- apscheduler 3.3.1
- pyintaller 3.2.1

### 问题描述

最近在项目中用到了apscheduler这个库，用来做定时任务非常的好，使用也很简单，不过在pyinstaller打包工程的时候，出现了如下错误

![pyinstaller_apscheduler_error](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/python/pyinstaller_apscheduler_01.png)

### 解决方法

编辑文件/usr/local/lib/python3.5/dist-packages/apscheduler/\_\_init\_\_.py

```
# These will be removed in APScheduler 4.0.
# release = __import__('pkg_resources').get_distribution('APScheduler').version.split('-')[0]
# version_info = tuple(int(x) if x.isdigit() else x for x in release.split('.'))
# version = __version__ = '.'.join(str(x) for x in version_info[:3])

release = (3,3,1) 
version_info = '3.3.1' 
version = '3.3.1'
```

3.3.1是我安装的apscheduler的版本号，视实际情况修改。

接下来创建一个名称为hook-ctypes.macholib.py，内容如下

```
# -*- coding: utf-8 -*-

from PyInstaller.utils.hooks import copy_metadata

datas = copy_metadata('apscheduler')
```

打包命令

```
pyinstaller -F --clean --additional-hooks-dir hooks --distpath release main.py
```

### 参考资料

1、<https://pypi.python.org/pypi/APScheduler>

2、<https://github.com/agronholm/apscheduler/issues/131>