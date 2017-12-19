### 软硬件环境
* Macbook Pro MGX 72
* OS X 优胜美地

### 前言
使用苹果Mac OSX系统已经有一段时间了，对于开发者来说，Mac OSX系统绝对是值得一玩的系统，只是成本可能会有点高些。本文针对新装系统进行配置，主要是针对开发者。老手请无视。

### 设置系统环境变量PATH
习惯了命令行操作的童鞋们，新建~/.bash_profile文件，加入如下语句（路径要自行修改）

	export PATH=/Users/djstava/Library/Android/sdk/platform-tools:$PATH

如果不想再使用这个，直接删除.bash_profile文件就可以了。

### 安装Homebrew

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

### 利用Homebrew安装软件

	brew install wget
	brew install subversion
	brew install git

### Apache
Mac osx默认已经安装了apache，需要使用的话，启动下就可以了。

	sudo apachectl start/restart/stop

其配置文件位于/etc/apache2/httpd.conf，默认的根目录位于/Library/WebServer/Documents/，可以自行修改路径，方便操作。

### Php
在配置完Apache的基础上，设置php的环境
	sudo vi /etc/apache2/httpd.conf
保证以下这句打开

	LoadModule alias_module libexec/apache2/libphp5.so

然后重启下apache就可以使用php了。

### Mysql
通过homebrew安装

	brew install mysql

启动mysql.server

	mysql.server start/restart/stop

连接mysql

	mysql -uroot

设置mysql root密码为djstava（可为任意）

	mysqladmin -u root password djstava

### 安装python django环境

	brew install pip
	pip install Django==1.7.3

测试django

	django-admin.py startproject testLocalHost
	cd testLocalhost
	python manage.py runserver

打开浏览器，输入localhost:8000进行查看

### 推荐App
* IDE

	Xcode:	开发Mac/iOS应用的首选    
	JetBrains系:	如PyCharm、PhpStorm、WebStorm、RubyMine等，看软件名字就知道是用来编写什么语言的了    
	Eclipse:	扩展能力超强的集成开发环境    
	Android studio:	开发android应用的神器，google出品，大有取代eclipse之势  

* 工具

	iTerm 2: 终端工具    
	MacDown: MarkDown编辑工具    
	Mou: MarkDown编辑工具，使用中发现command+s时特别卡
	Typora: MarkDown编辑器，目前主要用它，跨平台更方便
	GitHub: 开发者必备    
	Wireshark: 网络抓包必备    
	OmniGraffle: 思维导图        
	diffMerge: 文件比较工具    
	Charles: 抓包工具    
	Evernote: 大名鼎鼎的笔记工具    
	Cornerstone: svn客户端    
	Genymotion: android模拟器    
	JSON Accerator: JSON工具    
	IconKit: Android/iOS切图工具，根据原始图生成套图适配屏幕
	Sketch: 做图工具

* 办公

	iWork: 苹果已经提供免费下载    
	MS Office: 这个大家都应该知道了    
	Thunderbird: 邮件客户端    
	unibox: 邮件客户端    

* 社交

	QQ:     
	微信:                
	Twitter:   
	Skype:    

* 娱乐

	MPlayerX: 播放器    
	Vlc: 万能播放器    

* 效率

	Alfred: 强烈推荐，隐藏Dock，调出App神器,查找文件也很方便    
	The Unarchiver: 压缩/解压缩    
	Thunder: 迅雷下载        
	Caffeine:    
	Skitch: 抓图        
	LICEcap: GIF抓图
	gifox: gif生成工具
	1Password: 密码管理   

* 阅读

	Kindle: 阅读器    
	Reeder: rss客户端   
	iDocument:    
	Dash: 这个要强烈推荐下，开发者的最爱   
