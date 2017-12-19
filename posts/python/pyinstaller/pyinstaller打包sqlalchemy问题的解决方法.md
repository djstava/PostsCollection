### 软硬件环境

* python3
* sqlalchemy
* pyintaller 3.2.1

### 问题描述

最近在用pyinstaller打包一个使用了sqlalchemy库的python3工程中，碰到了打包后的可执行文件报错的问题，如下图所示

![pyinstaller_sqlalchemy_error](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/python/pyinstall_sqlalchemy_01.png)

### 解决方案

将sqlalchemy.dialects中的mysql引入，然后采用mysql+pymysql的方式打开数据库，代码如下

```
import pymysql

from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.dialects import mysql

username = 'djstava'
passwd = '123456'
server = '192.168.0.100'
port = '3306'
dbname = 'djdb'

cmd_connect = 'mysql+pymysql://{}:{}@{}:{}/{}'.format(username, passwd, server, port, dbname)

engine = create_engine(cmd_connect)
```

pyinstaller打包命令

```
pyinstaller -F --clean --distpath shark main.py
```

其中shark是目标文件夹，main.py是工程入口文件，-F表示生成一个可执行文件．

### 参考资料

* <http://blog.csdn.net/Xiong_Big/article/details/54614231>

