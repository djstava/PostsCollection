### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* PyQt 5.5.1
* PyCharm 5.0.1

### 前言
Qt是一个开源的跨平台的GUI框架，为很多计算机语言提供了应用程序开发接口，另外还提供了集成开发环境QtCreator、UI制作工具QtDesigner，使用起来既简单方便，又可以提升开发的速度。本文完成Qt5基于Python3.5.1的开发环境的搭建。

### 安装开发环境
#### Python 3.5.1
下载地址[https://www.python.org/downloads/mac-osx/][1]，点击完成安装。

#### Sip 4.17
下载地址[https://www.riverbankcomputing.com/software/sip/download][2]

编译安装步骤

	tar xvf sip-4.17.tar.gz
	cd sip-4.17
	python3.5 configure.py -d /Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages
	make
	sudo make install

#### PyQt 5.5.1
下载地址[https://riverbankcomputing.com/software/pyqt/download5][3]

编译安装步骤

	tar xvf PyQt-gpl-5.5.1.tar.gz
	cd PyQt-gpl-5.5.1
	python3.5 configure.py --qmake ~/Workshop/Qt5.5.1/5.5/clang_64/bin/qmake --disable=QtPositioning -d /Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages
	make
	sudo make install

--qmake选项制定qmake的路径，需要你自行修改；另外编译时加上了编译选项disable-QtPositioning，如果不加，会出现编译错误

![][image-1]

如果你的程序需要用到这个模块，可以自己新建一个头文件，具体请参考stackoverflow里的这条[http://stackoverflow.com/questions/33446131/pyqt5-error-during-python3-configure-py-fatal-error-qgeolocation-h-file-no][4]

#### 测试开发环境
在Terminal里敲下以下代码，如果没有报错就说明安装成功了。

`python3.5 -c "import PyQt5"`

### 在PyCharm里使用
PyCharm是时下最流行的Python IDE了，来自JetBrains公司，他们旗下还有其它非常优秀的IDE，有兴趣的都可以去尝试一下。下载地址[https://www.jetbrains.com/pycharm/?fromMenu][5]，安装完毕，打开Preferences—\>Project Interpreter选择咱们刚刚安装的Python3.5.1，点击OK。

![][image-2]

### 吐槽CSDN Markdown编辑器
使用过程发现2个问题
1、使用两减号时，自动合并成一个
2、在代码块中，如果代码过长，会被截取
一般情况下，我都是离线用Mac下的Ulysses工具编写，然后登录CSDN，导入。有知道上述问题的解决方案的朋友，请告诉我，谢谢。
### 参考文献
* [http://www.qt.io][6]
* [http://www.pythonschool.net/pyqt/installing-pyqt-on-mac-os-x/][7]
* [http://blog.15-cm.com/2015/09/13/pyqt5-pycharm/][8]
* [http://pyqt.sourceforge.net/Docs/PyQt5/installation.html#co-existence-with-pyqt4][9]

[1]:	https://www.python.org/downloads/mac-osx/
[2]:	https://www.riverbankcomputing.com/software/sip/download
[3]:	https://riverbankcomputing.com/software/pyqt/download5
[4]:	http://stackoverflow.com/questions/33446131/pyqt5-error-during-python3-configure-py-fatal-error-qgeolocation-h-file-no
[5]:	https://www.jetbrains.com/pycharm/?fromMenu
[6]:	http://www.qt.io
[7]:	http://www.pythonschool.net/pyqt/installing-pyqt-on-mac-os-x/
[8]:	http://blog.15-cm.com/2015/09/13/pyqt5-pycharm/
[9]:	http://pyqt.sourceforge.net/Docs/PyQt5/installation.html#co-existence-with-pyqt4

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyqt5_01.png "PyQt5-QtPositioning-error"
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyqt5_02.png "PyQt5-PyCharm-interpreter"
