### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* PyQt 5.5.1
* PyCharm 5.0.1

### 前言
在PyQt5系列教程的第一篇[http://blog.csdn.net/djstavaV/article/details/50218157][1]，我们已经搭建好了开发环境，今天，我们就用Python开发第一个Qt GUI程序，让大家感受下Qt开发的魅力，熟悉下Qt开发GUI程序的一般流程。

制作程序UI界面，一般会有2种方法，利用UI制作工具和纯代码编写，像移动开发中的Android和iOS都是这样，在PyQt5中，我们也有这么2种方式。

### QtDesigner工具简介
QtDesigner是专门用来制作Qt程序UI界面的工具，它使用起来非常简单，只要通过拖拽和点击就可以完成复杂的界面设计，而且还可以随时预览查看效果图。

![][image-1]

其中，区域1是UI界面制作导向，QtDesigner为我们提供了一些常用模块，非常方便；区域2是UI控件列表；区域3是控件属性列表；区域4是Action Editor编辑列表；区域5信号和槽的编辑列列表；区域6是资源处理窗口。

### 第一个PyQt5程序
说了这么多，还不如来动手实践一下。

![][image-2]

这个是我们第一个PyQt5项目的界面效果图，需要实现的是当点击界面上的按钮时，弹出一个提示框，提示框内显示一串文字。

好了，目标定好了，马上开始去实现它。

#### QtDesigner设计UI
创建一个基于Main Window模块的UI文件，取名叫firstPyQt5.ui。从Widget Box中找到(也可以)Push Button，将它拖拽到工作空间里，调整位置，在Button上输入文字，调整字体及大小，这些都可以在Property Editor里操作。按钮点击弹框的操作会牵出Qt里非常重要的一对概念，即信号和槽(signal and slot)，关于这个我打算再开一篇博文细讲。现在你只需要知道slot就是一个函数，如果一个signal和一个slot绑定以后，那么signal被触发，slot就会被执行。

那么问题来了，怎么在QtDesigner里绑定signal和slot呢？打开菜单栏里的Edit—\>Edit Signals/Slots，然后光标移到按钮上点击拖拽，这时会弹出一个编辑框

![][image-3]

因为是点击，所以signal选择clicked()，slot函数现在还没有，我们就点击Edit新建一个，叫firstPyQt5\_button\_click()

![][image-4]

![][image-5]

为了演示一下资源文件的使用，这里导入两张图片，一张给主窗口，另一张给帮助菜单项里的Action。

在QtDesigner引用资源文件之前，需要先准备一个qrc文件，它跟xml文件比较类似，用来制定资源文件的路径

	<RCC version="1.0">
	    <qresource>
	        <file>qt.png</file>
	        <file>penguin.jpg</file>
	    </qresource>
	</RCC>

接下来就可以在QtDesigner的Resource Browser里倒入qrc文件，这样qrc文件中描述的资源就可以使用了

#### 将firstPyQt5.ui转换成python代码
非常简单，通过Qt提供的命令行工具pyuic5就可以轻松实现

	pyuic5 -o firstPyQt5.py firstPyQt5.ui

#### 创建PyCharm工程
新建一个新的python文件main.py,代码如下

	# -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	import sys

	from PyQt5.QtWidgets import QApplication , QMainWindow

	from firstPyQt5 import *

	if __name__ == '__main__':
	    '''
	    主函数
	    '''

	    app = QApplication(sys.argv)
	    mainWindow = QMainWindow()
	    ui = Ui_mainWindow()
	    ui.setupUi(mainWindow)
	    mainWindow.show()
	    sys.exit(app.exec_())

接下修改下firstPyQt5.py文件，主要是去实现slot函数，因为之前在QtDesigner里没有实现，让它弹出一个消息框

	def firtPyQt5_button_click(self):
	    QtWidgets.QMessageBox.information(self.pushButton,"标题","这是第一个PyQt5 GUI程序")

最后运行下工程

![][image-6]

![][image-7]

这里一般将ui文件单独存成一个文件，便于更新。

### 源码下载

[http://download.csdn.net/detail/djstavav/9351205][2]

[1]:	http://blog.csdn.net/djstavaV/article/details/50218157
[2]:	http://download.csdn.net/detail/djstavav/9351205

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pyqt5_designer_01.png "designer_view"
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_01.png "firstPyQt5 overview"
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_ui_signal_02.png "QtDesigner-signal-slot"
[image-4]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_ui_signal_03.png "QtDesigner_signal_slot_2"
[image-5]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_ui_signal_04.png
[image-6]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_02.png "firstPyQt_02"
[image-7]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/firstPyQt5_03.png
