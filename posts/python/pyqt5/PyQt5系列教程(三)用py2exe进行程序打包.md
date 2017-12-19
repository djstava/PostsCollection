### 软硬件环境
* Windows 7
* Python 3.4.2
* PyQt5
* Py2exe

### 前言
在我们开发了完整的PyQt5项目后，一般都会将其打包成exe文件，方便其他人使用。今天我们就用Py2exe这个工具来打包上一堂课中完成的工程，工程源码在[http://download.csdn.net/detail/djstavav/9351205](http://download.csdn.net/detail/djstavav/9351205 "firstPyQt5")。

### Py2exe
py2exe是python的一个打包成exe的工具，官方提供的可执行文件还不支持python3。google一下，发现已经有好心人重编了源码来支持python3，我收集了放到了csdn下载频道，地址是[http://download.csdn.net/detail/djstavav/9354211](http://download.csdn.net/detail/djstavav/9354211 "py2exe下载地址")

### 打包firstPyQt5工程

#### 编写打包脚本
新建一个python文件setup.py，添加内容

    from distutils.core import setup
	import py2exe
	import sys

	py2exe_options = {
        "includes": ["sip"],
        "dll_excludes": ["MSVCP90.dll",],
        "compressed": 1,
        "optimize": 2,
        "ascii": 0,
        "bundle_files": 1,
        }

	setup(
      name = '第一个PyQt5程序',
      version = '1.0',
      windows = ['main.py'],
      zipfile = None,
      options = {'py2exe': py2exe_options}
      )

各个配置选项基本上都能看懂，就不说了

#### 运行打包命令
进入到你的工作目录，在cmd里执行

    python setup.py py2exe

![py2exe_cmd](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/py2exe_01.png)

执行完毕后，会在firstPyQt5文件下生成dist文件夹，进去执行生产的exe文件。很不幸，报错了

![py2exe_error_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/windows/PyQt5Fastboot/py2exe_error_01.png)

为了解决这个问题，我们添加一个环境变量

![py2exe_variant](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/windows/PyQt5Fastboot/py2exe_error_02.png)

再次执行exe，就看到我们预期的效果。

![py2exe_result](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/py2exe_01.png)
