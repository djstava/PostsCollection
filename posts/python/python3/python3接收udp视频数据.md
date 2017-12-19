### 软硬件环境

* ubuntu 16.04 64bit
* python3
* udp数据源

参考源码

```
# -*- coding: utf-8 -*-

"""
@author: djstava
@license: MIT Licence 
@contact: djstava@gmail.com
@site: http://www.xugaoxiang.com
@software: PyCharm
@file: main.py
@time: 8/15/17 5:58 PM
"""

import os
import sys
import socket
import struct

# 组播地址
ip = '225.0.0.1'

# 组播端口
port = 9001

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, socket.IPPROTO_UDP)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# bind的参数是一个元祖tuple
sock.bind((ip, port))

sock_value = struct.pack('4sl', socket.inet_aton(ip), socket.INADDR_ANY)

# 加入udp组播组
sock.setsockopt(socket.IPPROTO_IP, socket.IP_ADD_MEMBERSHIP, sock_value)

fileName = 'test.ts'

# 打开文件
fd = open(fileName, 'ab')

while True:
	data = sock.recv(1024000)
	fd.write(data)

# 关闭文件
fd.flush()
fd.close()

# 释放socket
sock.close()
```

