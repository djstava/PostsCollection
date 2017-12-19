### 软硬件环境

* Windows 10
* Python 3.5.1
* mysql 5.6
* sqlalchemy

### 前言

前文[python3中的mysql数据库操作](http://www.xugaoxiang.com/blog/index.php/archives/3/)已经介绍过利用pymysql来操作mysql数据库，本文介绍一个ORM框架SQLAlchemy，它可以帮助我们更加优雅、更加高效的实现数据库操作，而且还不限于mysql。

### 什么是ORM

ORM即Object Rational Mapping,对象关系映射。百度百科中是这样解释的

> 对象-关系映射（OBJECT RELATIONAL MAPPING，简称ORM），是随着面向对象的软件开发方法发展而产生的。用来把对象模型表示的对象映射到基于SQL的关系模型数据库结构中去。这样，我们在具体的操作实体对象的时候，就不需要再去和复杂的SQL语句打交道，只需简单的操作实体对象的属性和方法。ORM技术是在对象和关系之间提供了一条桥梁，前台的对象型数据和数据库中的关系型的数据通过这个桥梁来相互转化。

用一句话来概括就是，把关系数据库的表结构映射到对象上。操作对象的属性就等于操作数据库中的记录。

### SQLAlchemy实践

在写代码之前，我们先准备下数据库，这里还是以mysql为例，其它的数据库也类似，可以参靠sqlalchemy的官方文档。利用mysql-workbench图形化工具创建，表结构如下图所示，

![sqlalchemy_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/sqlalchemy_01.png)

接下来，安装相关的工具包

```
pip3 install mysql-connector
pip3 install pymsql
pip3 install sqlalchemy
```

最后来写代码

```
# -*- coding: utf-8 -*-

"""
@version: ??
@author: djstava
@license: MIT Licence 
@contact: djstava@gmail.com
@site: http://www.xugaoxiang.com/blog
@software: PyCharm
@file: SQLAlchemy.py
@time: 2017/3/22 10:42
"""

from sqlalchemy import Column, String, create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base

# 创建对象的基类:
Base = declarative_base()

# 定义Channel对象:
class Channel(Base):
	
	# 表名
	__tablename__ = 'playback'
	
	# 表结构
	id = Column(String(20),primary_key=True)
	channel_name = Column(String(45))
	address = Column(String(80))
	service_name = Column(String(45))
    
	def __init__(self,id,channel_name,address,service_name):
		self.id = id
		self.channel_name = channel_name
		self.address = address
		self.service_name = service_name

# 初始化数据库连接,:
engine = create_engine('mysql+mysqlconnector://root:toor@localhost:3306/troutling')

# 创建DBSession类型:
DBSession = sessionmaker(bind=engine)

session = DBSession()

# 增操作,增加3条记录
item1 = Channel(id='1',channel_name='catv1',address='http://10.10.10.188/catv1',service_name='news')
session.add(item1)

item2 = Channel(id='2',channel_name='catv2',address='http://10.10.10.188/catv2',service_name='sports')
session.add(item2)

item3 = Channel(id='3',channel_name='catv3',address='http://10.10.10.188/catv3',service_name='economics')
session.add(item3)

session.commit()
session.close()

# 查操作
session1 = DBSession()
channel = session1.query(Channel).filter(Channel.id < '4').all()

for i in range(len(channel)):
	print(channel[i].id)
	print(channel[i].channel_name)
	print(channel[i].address)
	print(channel[i].service_name)

session1.close()

# 改操作，将id为2的记录的service_name字段修改为movie
session2 = DBSession()
session2.query(Channel).filter(Channel.id == '2').update({Channel.service_name: 'movie'}, synchronize_session=False)
session2.commit()
session2.close()

## 查看修改结果
session3 = DBSession()
print('\n')
print(session3.query(Channel).filter(Channel.id == '2').one().service_name)
session3.close()

# 删操作，删除id为3的记录
session4 = DBSession()
session4.query(Channel).filter(Channel.id == '3').delete()
session4.commit()
session4.close()

```

执行上述脚本得到

```
D:\tools\python3.5.1\python.exe D:/workshop/github/ffmpegWrapper/SQLAlchemy.py
1
catv1
http://10.10.10.188/catv1
news
2
catv2
http://10.10.10.188/catv2
sports
3
catv3
http://10.10.10.188/catv3
economics


movie

Process finished with exit code 0
```

然后用mysql-workbench查看数据库

![sqlalchemy_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/sqlalchemy_02.png)

数据库中的记录跟代码要实现的意图完全相符。

### 参考资料

1. http://www.sqlalchemy.org/
2. http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014320114981139589ac5f02944601ae22834e9c521415000
3. http://baike.baidu.com/link?url=enj6JgEqdyte0DLpJqBAfiKusns6yL3-MHU8pW6_j14sa0yAvuTtC3DMlkHKbasHOti50UbcSLxljOVm5w6fV_