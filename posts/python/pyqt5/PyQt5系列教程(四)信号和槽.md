### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* PyQt 5.5.1

### 前言
信号(Signal)和槽(Slot)是Qt编程中对象间通讯的机制。在Qt中，每一个QObject对象，包括PyQT中所有继承自QWidget的控件(因为它们都是QObject的子对象)都支持signal和slot机制。

### signal和slot绑定
还是以之前的firstPyQt5工程为例，

	self.pushButton.clicked.connect(self.firtPyQt5_button_click)

上面的代码表明当按钮pushButton(QPushButton是QWidget的子类)被点击(释放clicked信号)时，slot(firtPyQt5\_button\_click)会被执行。

### 同一signal绑定多slot
PyQt5允许一个signal同时绑定多个slot，如

	self.pushButton.clicked.connect(self.firtPyQt5_button_click)
	self.pushButton.clicked.connect(self.firtPyQt5_button_click_2)

从执行结果上看，slot执行的顺序是跟绑定的顺序一样的，即先绑先执行。另外对于多个signal绑定同一slot的情况，比较容易理解，就不多说

### 一个signal绑定另一个signal
还有种特殊的情况是signal和signal绑定，这里假设还有一个QPushButton pushButton1,我们让它的clicked信号和pushButton的信号clicked相绑定，如下

	self.pushButton.clicked.connect(self.firtPyQt5_button_click)
	self.pushButton1.clicked.connect(self.pushButton.clicked)

### signal的解绑
PyQt5提供了disconnect方法来进行解绑

	self.pushButton1.clicked.disconnect(self.pushButton.clicked)
	self.pushButton.clicked.disconnect(self.firtPyQt5_button_click_2)

### 自定义signal
PyQt5提供了QtCore.pyqtSignal函数来产生一个signal,之后就是熟悉的绑定操作了，看个实例就清楚了

	class EmitSignal(QWidget):
		def __init__(self):
			super(EmitSignal,self).__init__()
			self.customSignal = pyqtSignal()

		def emitCustomSignal(self):
			self.customSignal.emit()
