### 软硬件环境
* OS X EI Capitan
* Python 3.5.1
* mysql 5.6

### 前言
在开发中经常涉及到数据库的使用，而python对于数据库也有多种解决方法。本文以python3中的mysql为例，介绍pymysql模块的使用。

### 准备数据库
创建一个mysql数据库，名字叫testdb,建立一张表叫testtable，它有3个字段，分别是id，数据类型是INT(11)，设为主键、非空、UNSIGNED、AUTO INCREMENT，name，数据类型是VARCHAR(45),设为非空、唯一，sex，数据类型是VARCHAR(45)，设为非空

### python3 源码
	# -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	import logging
	import pymysql

	class MySQLCommand(object):
	    def __init__(self,host,port,user,passwd,db,table):
	        self.host = host
	        self.port = port
	        self.user = user
	        self.password = passwd
	        self.db = db
	        self.table = table

	    def connectMysql(self):
	        try:
	            self.conn = pymysql.connect(host=self.host,port=self.port,user=self.user,passwd=self.password,db=self.db,charset='utf8')
	            self.cursor = self.conn.cursor()
	        except:
	            print('connect mysql error.')

	    def queryMysql(self):
	        sql = "SELECT * FROM " + self.table

	        try:
	            self.cursor.execute(sql)
	            row = self.cursor.fetchone()
	            print(row)

	        except:
	            print(sql + ' execute failed.')

	    def insertMysql(self,id,name,sex):
	        sql = "INSERT INTO " + self.table + " VALUES(" + id + "," + "'" + name + "'," + "'" + sex + "')"
	        try:
	            self.cursor.execute(sql)
	        except:
	            print("insert failed.")

	    def updateMysqlSN(self,name,sex):
	        sql = "UPDATE " + self.table + " SET sex='" + sex + "'" + " WHERE name='" + name + "'"
	        print("update sn:" + sql)

	        try:
	            self.cursor.execute(sql)
	            self.conn.commit()
	        except:
	            self.conn.rollback()


	    def closeMysql(self):
	        self.cursor.close()
	        self.conn.close()
