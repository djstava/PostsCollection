### 软硬件环境

* python3
* struct

### 简介

struct是python(包括版本2和3)中的内建模块，它用来在c语言中的结构体与python中的字符串之间进行转换，数据一般来自文件或者网络。

### 常用方法

##### struct.pack(fmt,v1,v2,...)

返回的是一个字符串，是参数按照fmt数据格式组合而成。

##### struct.unpack(fmt,string)

按照给定数据格式解开(通常都是由struct.pack进行打包)数据，返回值是一个tuple

### 格式符

下面2张表来自官网

| Character | Byte order             | Size     | Alignment |
| --------- | ---------------------- | -------- | --------- |
| `@`       | native                 | native   | native    |
| `=`       | native                 | standard | none      |
| `<`       | little-endian          | standard | none      |
| `>`       | big-endian             | standard | none      |
| `!`       | network (= big-endian) | standard | none      |

| Format | C Type               | Python type        | Standard size | Notes    |
| ------ | -------------------- | ------------------ | ------------- | -------- |
| `x`    | pad byte             | no value           |               |          |
| `c`    | `char`               | string of length 1 | 1             |          |
| `b`    | `signed char`        | integer            | 1             | (3)      |
| `B`    | `unsigned char`      | integer            | 1             | (3)      |
| `?`    | `_Bool`              | bool               | 1             | (1)      |
| `h`    | `short`              | integer            | 2             | (3)      |
| `H`    | `unsigned short`     | integer            | 2             | (3)      |
| `i`    | `int`                | integer            | 4             | (3)      |
| `I`    | `unsigned int`       | integer            | 4             | (3)      |
| `l`    | `long`               | integer            | 4             | (3)      |
| `L`    | `unsigned long`      | integer            | 4             | (3)      |
| `q`    | `long long`          | integer            | 8             | (2), (3) |
| `Q`    | `unsigned long long` | integer            | 8             | (2), (3) |
| `f`    | `float`              | float              | 4             | (4)      |
| `d`    | `double`             | float              | 8             | (4)      |
| `s`    | `char[]`             | string             |               |          |
| `p`    | `char[]`             | string             |               |          |
| `P`    | `void *`             | integer            |               | (5), (3) |

### 实例

理论性的东西看起来都比较枯燥，来个实例代码就容易理解多了。本例来实现往一个2进制文件中按照某种特定格式写入数据，之后再将它读出。相信通过这个理例子，你就能基本掌握struct的使用。

```
# -*- coding: utf-8 -*-
__author__ = 'djstava'

'''
数据格式为
姓名 年龄 性别   职业
lily 18  female teacher
'''

import os
import struct

fp = open('test.bin','wb')

# 按照上面的格式将数据写入文件中
# 这里如果string类型的话，在pack函数中就需要encode('utf-8')
name = b'lily'
age = 18
sex = b'female'
job = b'teacher'

# int类型占4个字节
fp.write(struct.pack('4si6s7s', name,age,sex,job))
fp.flush()
fp.close()

# 将文件中写入的数据按照格式读取出来
fd = open('test.bin','rb')
# 21 = 4 + 4 + 6 + 7
print(struct.unpack('4si6s7s',fd.read(21)))
fd.close()
```

运行上面的代码，可以看到读出的数据与写入的数据是完全一致的。

```
/Library/Frameworks/Python.framework/Versions/3.5/bin/python3.5 /Users/djstava/Playground/flaskPy/test.py
(b'lily', 18, b'female', b'teacher')

Process finished with exit code 0
```

### 参考资料

1、<https://docs.python.org/2/library/struct.html>