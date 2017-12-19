###Mac安装pycurl
源码下载地址<http://pycurl.sourceforge.net>

	tar xvf pycurl-7.19.5.tar.gz    
	cd pycurl-7.19.5    
	sudo python setup.py install    

测试pycurl是否安装成功	，命令行执行python	
​	 
	import pycurl	
	print pycurl.version	

如果你使用是pycharm，可以直接到preference里安装，如下图 

![pycurl_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pycurl_01.png)

###实例
这里的示例，实现一个从web server上下载一个文件到本地的功能，利用pycurl这个模块，直接上代码   

	__author__ = 'djstava'    

	#!/usr/bin/env python
	#coding=utf-8
	
	import pycurl
	import sys
	
	DJ_PYCURL_CONNECTTIMEOUT = 30
	DJ_PYCURL_TIMEOUT = 300
	DJ_PYCURL_DOWNLOADURL = "http://192.168.1.99/test.zip"
	DJ_PYCURL_DOWNLOAD_FILE = "download.file"
	
	fp = open(DJ_PYCURL_DOWNLOAD_FILE,'wb+')
	
	def dj_pycurl_writeFile(buffer):
		fp.write(buffer)
	
	def dj_pycurl_download(url):
		pycurl.global_init(pycurl.GLOBAL_ALL)
		c = pycurl.Curl()
	
		c.setopt(pycurl.URL, url)
		c.setopt(pycurl.WRITEDATA,fp)
	    c.setopt(pycurl.WRITEFUNCTION,dj_pycurl_writeFile)
		c.setopt(pycurl.NOPROGRESS,0)
		c.setopt(pycurl.CONNECTTIMEOUT,DJ_PYCURL_CONNECTTIMEOUT)
		c.setopt(pycurl.TIMEOUT,DJ_PYCURL_TIMEOUT)
		c.setopt(pycurl.VERBOSE,1)
	
		c.perform()
		c.close()
		fp.close()
	
	if __name__ == '__main__':
		dj_pycurl_download(DJ_PYCURL_DOWNLOADURL)
###代码测试

![pycurl_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/pycurl_02.png)

###Mac安装nginx
brew install nginx
####修改nginx配置文件
/usr/local/etc/nginx/nginx.conf
####nginx默认根目录
/usr/local/opt/nginx/html
####启动nginx服务
sudo nginx

###References
1、<http://pycurl.sourceforge.net>
