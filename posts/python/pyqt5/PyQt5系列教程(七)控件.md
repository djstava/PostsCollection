### 软硬件环境
* Windows 10
* Python 3.4.2
* PyQt 5.5.1
* PyCharm 5.0.4

### 前言
控件是PyQt应用程序的基石。PyQt5自带很多不同的控件，包括像button、check box、slider等等。在本节中我们将学习几个非常有用的控件:QCheckBox、ToggleButton、QSlider、QProgressBar和QCalendarWidget。

### QCheckBox
一个QCheckBox会有2种状态:选中和为选中。它有一个选择框和一个label组成，它常常被用来表示应用的某些特性是可启用或不启用的。

    # -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	"""
	ZetCode PyQt5 tutorial

	In this example, a QCheckBox widget
	is used to toggle the title of a window.

	author: Jan Bodnar
	website: zetcode.com
	last edited: January 2015
	"""

	import sys
	from PyQt5.QtWidgets import QWidget, QCheckBox, QApplication
	from PyQt5.QtCore import Qt


	class Example(QWidget):

    	def __init__(self):
    	    super().__init__()

    	    self.initUI()


    	def initUI(self):      

    	    cb = QCheckBox('Show title', self)
    	    cb.move(20, 20)
    	    cb.toggle()
    	    cb.stateChanged.connect(self.changeTitle)

    	    self.setGeometry(300, 300, 250, 150)
    	    self.setWindowTitle('QCheckBox')
    	    self.show()


    	def changeTitle(self, state):

    	    if state == Qt.Checked:
    	        self.setWindowTitle('QCheckBox')
    	    else:
    	        self.setWindowTitle('')


	if __name__ == '__main__':

    	app = QApplication(sys.argv)
    	ex = Example()
    	sys.exit(app.exec_())


程序执行效果如下

![qcheckbox_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/qcheckbox_01.png)

在这个例子中，我们创建了一个选择框，它状态的变化会引起窗口标题的变化。

    cb = QCheckBox('Show title', self)

这条语句是QCheckBox的构造函数，它的label是"Show title"。

    cb.toggle()

设置默认是选中状态

    cb.stateChanged.connect(self.changeTitle)

将自定义的槽函数changeTitle和信号stateChanged绑定起来，槽函数changeTitle会改变窗口的标题

    def changeTitle(self, state):

    if state == Qt.Checked:
        self.setWindowTitle('QCheckBox')
    else:
        self.setWindowTitle('')

选中状态下，窗口标题设置为"QCheckBox",否则设置为空

### Toggle button
Toggle button是一种特殊的QPushButton(按钮),它也有2种状态，按下和未按下，我们通过点击它来区分2种不同的状态。实际很多情况下都会用到它。

    # -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	"""
	ZetCode PyQt5 tutorial

	In this example, we create three toggle buttons.
	They will control the background colour of a
	QFrame.

	author: Jan Bodnar
	website: zetcode.com
	last edited: January 2015
	"""

	import sys
	from PyQt5.QtWidgets import (QWidget, QPushButton,
    	QFrame, QApplication)
	from PyQt5.QtGui import QColor


	class Example(QWidget):

    	def __init__(self):
    	    super().__init__()

    	    self.initUI()


    	def initUI(self):

    	    self.col = QColor(0, 0, 0)

    	    redb = QPushButton('Red', self)
    	    redb.setCheckable(True)
    	    redb.move(10, 10)

    	    redb.clicked[bool].connect(self.setColor)

    	    redb = QPushButton('Green', self)
    	    redb.setCheckable(True)
    	    redb.move(10, 60)

    	    redb.clicked[bool].connect(self.setColor)

    	    blueb = QPushButton('Blue', self)
    	    blueb.setCheckable(True)
    	    blueb.move(10, 110)

    	    blueb.clicked[bool].connect(self.setColor)

    	    self.square = QFrame(self)
    	    self.square.setGeometry(150, 20, 100, 100)
    	    self.square.setStyleSheet("QWidget { background-color: %s }" %
    	        self.col.name())

    	    self.setGeometry(300, 300, 280, 170)
    	    self.setWindowTitle('Toggle button')
    	    self.show()


    	def setColor(self, pressed):

    	    source = self.sender()

    	    if pressed:
    	        val = 255
    	    else: val = 0

    	    if source.text() == "Red":
    	        self.col.setRed(val)
    	    elif source.text() == "Green":
    	        self.col.setGreen(val)
    	    else:
    	        self.col.setBlue(val)

    	    self.square.setStyleSheet("QFrame { background-color: %s }" %
    	        self.col.name())


	if __name__ == '__main__':

    	app = QApplication(sys.argv)
    	ex = Example()
    	sys.exit(app.exec_())


代码执行效果如下所示

![toggleButton_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/toggleButton_01.png)

本例中我们创建了3个toggle button和一个QWidget，将QWidget的背景色设置成了黑色，左边的3个toggle button会改变右边QWidget的颜色。

    redb = QPushButton('Red', self)
	redb.setCheckable(True)

为了创建toggle button,我们首先创建一个QPushButton，然后调用方法setCheckable()。后面的信号和槽的绑定，相信大家都很熟悉了，这里就不说了。

### QSlider
QSlider是一个滑动条，允许你在某一范围内拖动。有时候这种方式比输入数字或者使用spin box更加自然。

	# -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	"""
	ZetCode PyQt5 tutorial

	This example shows a QSlider widget.

	author: Jan Bodnar
	website: zetcode.com
	last edited: January 2015
	"""

	import sys
	from PyQt5.QtWidgets import (QWidget, QSlider,
    	QLabel, QApplication)
	from PyQt5.QtCore import Qt
	from PyQt5.QtGui import QPixmap


	class Example(QWidget):

    	def __init__(self):
        	super().__init__()

        	self.initUI()


    	def initUI(self):      

        	sld = QSlider(Qt.Horizontal, self)
        	sld.setFocusPolicy(Qt.NoFocus)
        	sld.setGeometry(30, 40, 100, 30)
        	sld.valueChanged[int].connect(self.changeValue)

        	self.label = QLabel(self)
        	self.label.setPixmap(QPixmap('mute.png'))
        	self.label.setGeometry(160, 40, 80, 30)

        	self.setGeometry(300, 300, 280, 170)
        	self.setWindowTitle('QSlider')
        	self.show()


    	def changeValue(self, value):

        	if value == 0:
            	self.label.setPixmap(QPixmap('mute.png'))
        	elif value > 0 and value <= 30:
            	self.label.setPixmap(QPixmap('min.png'))
        	elif value > 30 and value < 80:
            	self.label.setPixmap(QPixmap('med.png'))
        	else:
            	self.label.setPixmap(QPixmap('max.png'))


	if __name__ == '__main__':

    	app = QApplication(sys.argv)
    	ex = Example()
    	sys.exit(app.exec_())

代码运行效果如下所示，我这没有加入图片，所以看不到右侧图片的变化。我这里只解释下QSlider的使用，信号和曹函数跟之前都是一样的。

![qslider_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/qslider_01.png)

    sld = QSlider(Qt.Horizontal, self)
    sld.setFocusPolicy(Qt.NoFocus)
    sld.setGeometry(30, 40, 100, 30)
    sld.valueChanged[int].connect(self.changeValue)

QSlider构造函数中传入Qt.Horizontal，创建的是一个横向的slider

### QProgressBar
ProgressBar一般用来表示任务的进度，也有横向和众向之分。你可以设置进度条的最小值和最大值，默认是0和99。

    # -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	"""
	ZetCode PyQt5 tutorial

	This example shows a QProgressBar widget.

	author: Jan Bodnar
	website: zetcode.com
	last edited: January 2015
	"""

	import sys
	from PyQt5.QtWidgets import (QWidget, QProgressBar,
    	QPushButton, QApplication)
	from PyQt5.QtCore import QBasicTimer


	class Example(QWidget):

    	def __init__(self):
    	    super().__init__()

    	    self.initUI()


    	def initUI(self):

    	    self.pbar = QProgressBar(self)
    	    self.pbar.setGeometry(30, 40, 200, 25)

    	    self.btn = QPushButton('Start', self)
    	    self.btn.move(40, 80)
    	    self.btn.clicked.connect(self.doAction)

    	    self.timer = QBasicTimer()
    	    self.step = 0

    	    self.setGeometry(300, 300, 280, 170)
    	    self.setWindowTitle('QProgressBar')
    	    self.show()


    	def timerEvent(self, e):

    	    if self.step >= 100:
    	        self.timer.stop()
    	        self.btn.setText('Finished')
    	        return

    	    self.step = self.step + 1
    	    self.pbar.setValue(self.step)


    	def doAction(self):

    	    if self.timer.isActive():
    	        self.timer.stop()
    	        self.btn.setText('Start')
    	    else:
    	        self.timer.start(100, self)
    	        self.btn.setText('Stop')


	if __name__ == '__main__':

    	app = QApplication(sys.argv)
    	ex = Example()
    	sys.exit(app.exec_())

代码运行效果如下所示

![qprogressbar_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/qprogressbar_01.png)

这里使用一个计时器来启动进度条

    self.timer = QtCore.QBasicTimer()

    def timerEvent(self, e):

    if self.step >= 100:

        self.timer.stop()
        self.btn.setText('Finished')
        return

    self.step = self.step + 1
    self.pbar.setValue(self.step)

每个QObject及其子类都有个timerEvent()事件处理器，需要我们重新实现，它的第一个参数是超时时间

### QCalendarWidget
QCalendarWidget是一个以月份为基础的日历控件，用来供用户选择日期

    # -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	"""
	ZetCode PyQt5 tutorial

	This example shows a QCalendarWidget widget.

	author: Jan Bodnar
	website: zetcode.com
	last edited: January 2015
	"""

	import sys
	from PyQt5.QtWidgets import (QWidget, QCalendarWidget,
    	QLabel, QApplication)
	from PyQt5.QtCore import QDate


	class Example(QWidget):

    	def __init__(self):
        	super().__init__()

        	self.initUI()


    	def initUI(self):

        	cal = QCalendarWidget(self)
        	cal.setGridVisible(True)
        	cal.move(20, 20)
        	cal.clicked[QDate].connect(self.showDate)

        	self.lbl = QLabel(self)
        	date = cal.selectedDate()
        	self.lbl.setText(date.toString())
        	self.lbl.move(130, 260)

        	self.setGeometry(300, 300, 350, 300)
        	self.setWindowTitle('Calendar')
        	self.show()


    	def showDate(self, date):

        	self.lbl.setText(date.toString())


	if __name__ == '__main__':

    	app = QApplication(sys.argv)
    	ex = Example()
    	sys.exit(app.exec_())


代码运行效果如下所示

![qcalendarwidget_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/qcalendarwidget_01.png)

### 参考文献
[http://zetcode.com/gui/pyqt5/widgets/](http://zetcode.com/gui/pyqt5/widgets/)
