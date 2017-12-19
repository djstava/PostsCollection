### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* PyQt 5.5.1

### 前言
如果需要在程序中周期性地进行某项操作，比如检测某种设备的状态，就会用到定时器。本文就来看看PyQT5中的QTimer的使用。

### QTimer示例
假设要实现每过一秒计数一次这个功能，来看看QTimer怎么实现？

	self.timer = QTimer(self)
	self.count = 0
	self.timer.timeout.connect(self.showNum)
	self.startCount()

首先实例化一个timer，然后将它的timeout信号和自定义的showNum（）方法绑定。最后调用startCount()这个方法开始这个定时器。上述几条语句一般放在window或者form的构造函数里。完整的例子自己去写写吧，也很简单。

startCount()方法
	def startCount(self):
		self.timer.start(1000)

timer.start()时间参数的单位是毫秒，这点要注意,相当于时间每过一秒，timer的timeout()就会被触发一次，而信号timeout又是和槽showNum()绑定在一起的，所以showNum（）也会被调用一次。

showNum()槽函数
	def showNum(self):
		count = count + 1
		print(count)
