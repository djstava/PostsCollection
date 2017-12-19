###软硬件环境
* ubuntu
* notify-osd
* libnotify-bin

###概述
D-Bus是一种消息总线系统，它为应用程序之间互相调用提供了更为简单的方法。本文中实现的桌面通知就是基于D-Bus机制。桌面通知在ubuntu系统的早期版本中就有应用，如常见的音量调节、thunderbird的来信通知、QQ消息通知等都是如此。

先给一张系统架构图，方便理解。

![notify_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/desktop_notification/ubuntu-desktop-notification_01.png)

为简单起见，在这里称请求方为Client，响应请求的为Server。

###Server
	sudo apt-get source notify-osd    
	cd notify-osd-0.9.24    
	vi notify-osd/src/default.c
	
	#define DEFAULT_DESKTOP_BOTTOM_GAP 6.0f
	#define DEFAULT_BUBBLE_WIDTH 24.0f
	#define DEFAULT_BUBBLE_MIN_HEIGHT 5.0f
	#define DEFAULT_BUBBLE_MAX_HEIGHT 12.2f
	#define DEFAULT_BUBBLE_VERT_GAP 0.5f
	#define DEFAULT_BUBBLE_HORZ_GAP 0.5f
	#define DEFAULT_BUBBLE_SHADOW_SIZE 0.7f
	#define DEFAULT_BUBBLE_SHADOW_COLOR "#000000"
	#define DEFAULT_BUBBLE_BG_COLOR "#131313"
	#define DEFAULT_BUBBLE_BG_OPACITY "#cc"
	#define DEFAULT_BUBBLE_HOVER_OPACITY "#66"
	#define DEFAULT_BUBBLE_CORNER_RADIUS 0.375f
	#define DEFAULT_CONTENT_SHADOW_SIZE 0.125f
	#define DEFAULT_CONTENT_SHADOW_COLOR "#000000"
	#define DEFAULT_MARGIN_SIZE 1.0f
	#define DEFAULT_ICON_SIZE 3.0f
	#define DEFAULT_GAUGE_SIZE 0.625f
	#define DEFAULT_GAUGE_OUTLINE_WIDTH 0.125f
	#define DEFAULT_TEXT_FONT_FACE "Sans"
	#define DEFAULT_TEXT_TITLE_COLOR "#ffffff"
	#define DEFAULT_TEXT_TITLE_WEIGHT TEXT_WEIGHT_BOLD
	#define DEFAULT_TEXT_TITLE_SIZE 1.0f
	#define DEFAULT_TEXT_BODY_COLOR "#eaeaea"
	#define DEFAULT_TEXT_BODY_WEIGHT TEXT_WEIGHT_NORMAL
	#define DEFAULT_TEXT_BODY_SIZE 0.9f
	#define DEFAULT_PIXELS_PER_EM 10.0f
	#define DEFAULT_SYSTEM_FONT_SIZE 10.0f
	#define DEFAULT_SCREEN_DPI 96.0f
	#define DEFAULT_GRAVITY GRAVITY_NORTH_EAST

####Note
	DEFAULT_GRAVITY可以取如下3个值
	* GRAVITY_NONE: the behaviour in Jaunty. A notification will always go into the top right corner.    
	
	* GRAVITY_EAST: the behaviour NotifyOsd has been having for about a week in Karmic's development cycle, with the notifications centered vertically on the screen.    
	
	* GRAVITY_NORTH_EAST: the Karmic behaviour. Works like GRAVITY_NONE but does not put asynchronous notifications on top of the screen, even if there is no synchronous notification being displayed.
	
	./configure    
	make    
	make install(if you need)    


###Client

	sudo apt-get install libnotify-bin


###Have a test
1. 启动server    

 notify-osd/src/notify-osd	

2. 修改文字显示大小    
 将 DEFAULT_TEXT_TITLE_SIZE 改为4.0f    

3. Client发送请求    

 notify-send "djstava" "I Love It"


![notify_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/desktop_notification/ubuntu-desktop-notification_02.png)

###桌面通知的python实现
1. 安装pynotify模块
 <http://www.galago-project.org/downloads.php>下载notify-python-0.1.1.tar.gz，解压然后进行安装三部曲./configure、make、sudo make install    

2. 模块的使用    

     try:
        	import pynotify
        	if pynotify.init('Fist Test'):
            	n = pynotify.Notification('Title','message')
            	n.show()
        	else:
            	print 'Pynotify module not initialized'
         except:
        	print 'Pynotify module not installed'
     ​     
     可以像下面一样来设置消息的紧急级别    

        n.set_urgency(pynotify.URGENCY_LOW)    
        n.set_urgency(pynotify.URGENCY_NORMAL)    
        n.set_urgency(pynotify.URGENCY_CRITICAL)    

    关闭消息通知操作

        n.close()

###参考资料
1、<http://kobesearch.cpan.org/htdocs/Desktop-Notify/Desktop/Notify.pm.html>    
2、<http://www.galago-project.org/specs/notification/0.9/index.html>    
3、<https://wiki.ubuntu.com/NotifyOSD>    
4、<http://doc.trolltech.com/4.2/intro-to-dbus.html>    
5、<http://www.freedesktop.org/wiki/Software/dbus>    