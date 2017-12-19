### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* PyQt 5.5.1
* PyCharm 5.0.3

### 前言
前面的内容我们介绍了利用QtDesigner来设计界面，再通过命令行工具pyuic5将ui文件转换成py源文件。不过由于要响应事件操作，网网会将相应的槽函数写在ui的py文件里，这样，界面和逻辑开发就混合在一起了，每一次的ui更新都会伴随着转换后py文件的修改，想想就知道烦人了。对于多人协作的项目，这样的效率肯定是低下的。本文就来介绍如何将这二者剥离。

### 实例讲解

#### 设计ui
首先用QtDesigner做一个简单的界面mainwindow.ui, 在菜单栏上加入几个子菜单

![][image-1]

#### ui转换成py
利用pyuic5进行转换

	pyuic5 -o ui_mainwindow.py mainwindow.ui

#### 剥离ui和逻辑
新建一个mainwindow.py文件,创建MainWindow类

	class MainWindow(QMainWindow, Ui_MainWindow):
	    def __init__(self, parent=None):
	        super(MainWindow, self).__init__(parent)
	        self.setupUi(self)

	        self.action_exit.triggered.connect(self.onExitTriggered)

	        self.action_copy.triggered.connect(self.onCopyTriggered)
	        self.action_paste.triggered.connect(self.onPasteTriggered)
	        self.action_cut.triggered.connect(self.onCutTriggered)

在这里去绑定相应的signal和slot，实现业务逻辑，代码结构也清晰多了。这样，以后如果再遇到ui更新了，我只需要将更新后的ui文件替换，在转换成py就可以了，实现了ui和逻辑的分离。

#### main函数中调用
在main中实例化MainWindow，调用show方法显示

	if __name__ == "__main__":
	    app = QApplication(sys.argv)
	    mainWindow = MainWindow()
	    mainWindow.show()
	    sys.exit(app.exec_())

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/divide_ui_src_01.png "ui"
