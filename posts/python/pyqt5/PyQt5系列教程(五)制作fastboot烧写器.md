### 软硬件环境
* Windows 7
* Python 3.4.2
* PyQt 5.5.1
* PyCharm 5.0.2

### 前言
fastboot是针对Android设备的一种刷机方式，它比recovery更底层，刷机速度更快。本文要完成的这个项目就是用PyQt5来给fastboot加个GUI层，使操作更加简单方便。

![PyQt5Fastboot\_OverView][image-1]

### 需求分析
首先要清楚fastboot能做些什么，通过在cmd里输入fastboot就可以查看它的帮助信息

![PyQt5 command][image-2]

本工程主要实现的是fastboot的烧录功能，其它的像获取、设置相关信息就没有涉及，有需要的话就自己去添加。

还有一个比较特殊的需求就是烧录的镜像需要修改的情况，我这里称它位BBCB，它的一部分数据由用户输入的。

### 界面设计
使用QtDesigner来设计，这里只用了label、pushButton、lineedit，还有就是水平布局、垂直布局和菜单栏，还是比较简单。

### 代码编写
#### 在QThread中更新UI
利用信号和槽机制。首先创建一个class，继承自QThread，在需要更新UI的时候发送一个信号

	class detectDeviceThread(QThread):

	    detectSignal = pyqtSignal(object)

	    def __int__(self):
	        QThread.__init__(self)

	    def run(self):
	        while True:
	            deviceNum = lj_list_device_id(self)
	            self.detectSignal.emit(deviceNum)
	            time.sleep(3)

在UI层面，绑定信号和槽，然后实现该槽，UI的更新就在槽里执行。

		def onDeviceDetected(self,data):
	        '''
	        检测到Android设备上线后的操作
	        :return:
	        '''

	        font = QtGui.QFont()
	        font.setFamily("微软雅黑 Light")
	        font.setPointSize(12)
	        font.setBold(True)
	        self.textBrowser_device_id.setFont(font)

	        if(common.PREVIOUS_DATA != data):
	            common.PREVIOUS_DATA = data
	            self.textBrowser_device_id.setText(data)
	            if not data.strip():
	                common.FLAG_DEVICE_ONLINE = False
	            else:
	                common.FLAG_DEVICE_ONLINE = True


	def startDetectDevice(self):
	        '''
	        开启线程，检测Android设备上线
	        :return:
	        '''
	        self.detectThread = detectDeviceThread()
	        self.detectThread.detectSignal.connect(self.onDeviceDetected)
	        self.detectThread.start()

#### struct处理二进制数据
我这里需要变更BBCB镜像的某几个字节(类似于C语言中的结构体数据类型)的数据，需要使用到struct这个模块。来看段代码

	fp = open("toc\\nvram.toc","rb+")
	fp.seek(common.BBCB_OFFSET + 2,os.SEEK_SET)
	        manufacturer_id = fp.read(1)
	        manufacturer_id_new = int(self.lineEdit_manu_id.text())
	        if(manufacturer_id != manufacturer_id_new):
	            logging.debug("newmanufacturer_id: " + str(manufacturer_id_new))
	            fp.seek(-1,os.SEEK_CUR)
	            fp.write(struct.pack("B",manufacturer_id_new))
	            fp.flush()

首先是打开文件，进行文件定位，然后将要写入的数据按照特定的格式写入文件。struct中支持的格式如下表所示

![struct format][image-3]

在上例中，是将manufacturer\_id\_new的值以格式”B”，也就是C语言里的unsigned char写入到文件中。依照上表，如果是一个长度为5的字符串，格式应该是”5s”

#### logging模块的使用
这里使用python自带的logging作为日志系统。因为需要在多个模块内(多个文件)使用，我们在工程入口处进行全局的初始化

	def initLogConfiguration():
	    '''
	    初始化日志配置
	    '''
	    logging.basicConfig(level = logging.DEBUG,
	                        filename = common.LOGFILE,
	                        filemode = 'a+',
	                        format = '%(asctime)s - %(filename)s - line %(lineno)-4d - %(levelname)s - %(message)s',
	                        datefmt = '%m-%d %H:%M')

这里是输出到文件，同样你也可以输出到控制台，通过stream参数指定，如果同时指定了filename和stream，logging会忽略stream。初始化完毕后，在其它需要输出log的模块就直接使用就可以了

	logging.debug("some message")

#### 跨平台
主要就是判断当前平台，然后进行不同方式的处理。这里使用platform库

	import platform

	PLATFORM_SYSTEM = platform.system()
	OS_MAC = (PLATFORM_SYSTEM == "Darwin")
	OS_WIN = (PLATFORM_SYSTEM == "Windows")
	OS_LINUX = (PLATFORM_SYSTEM == "Linux")

### 源码地址

[https://github.com/djstava/PyQt5Fastboot][1]

[1]:	https://github.com/djstava/PyQt5Fastboot "PyQt5Fastboot github"

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/windows/PyQt5Fastboot/PyQt5Fastboot_01.png
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/windows/PyQt5Fastboot/PyQt5Fastboot_02.png
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/struct_format.png
