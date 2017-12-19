### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* GCC 4.9

### 前言
最近在做python3开发中，碰到了一个问题，需要通过调用C的一个动态链接库来获取相应的值。扒了扒网络，动手实践了下，形成此文。

### 准备C动态库
源码test.c
	#include <stdio.h>
	#include <stdlib.h>

	char * printStr(const char *p,const char *q)
	{
	    printf("%s",p);
	    printf("%s",q);
	    return "djstava";
	}

通过以下命令编译成动态链接库
	gcc -fPIC -shared -o libtest.so test.c

### python3中调用
要调用C库中的函数，需要用到ctypes这个模块
	# -*- coding: utf-8 -*-
	__author__ = 'djstava'

	from ctypes import *

	handle = cdll.LoadLibrary('libtest.so')
	func = handle.printStr
	func.argtypes = (c_char_p,c_char_p)
	func.restype = c_char_p
	tmp = handle.printStr("hello".encode("utf-8"),"world".encode("utf-8"))
	print(tmp.decode("utf-8"))


程序执行结果
	helloworlddjstava

### 程序解释
	func.argtypes = (c_char_p,c_char_p)
	func.restype = c_char_p

这2句是分别设置参数数据类型和返回值类型，如果不进行设置，直接调用的话，参数可以正常接收，但是返回值永远是个int值，传入的字符串参数必须为encode(“utf-8”)，否则在c库中仅会打印为首字符

	handle = cdll.LoadLibrary('libtest.so')
	ret = handle.printStr("hello".encode("utf-8"),"world".encode("utf-8"))

关于其它数据类型的argtypes的设置，请查阅参考文献中的链接。
### 参考文献
[https://docs.python.org/3/library/ctypes.html][1]

[1]:	https://docs.python.org/3/library/ctypes.html
