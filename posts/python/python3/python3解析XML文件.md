### 软硬件环境
* Ubuntu 15.10 32bit
* Python 3.5.1
* PyQt 5.5.1

### 前言
Python解析XML的方法挺多，本文主要是利用ElementTree来完成。

### 实例讲解
#### 解析XML
以如下的XML文件为例

    <root>
    	<version>1.0.04</version>
    	<mysqlhost>10.10.10.240</mysqlhost>
    	<mysqlport>3306</mysqlport>
    	<mysqluser>root</mysqluser>
    	<mysqlpassword>123456</mysqlpassword>
    	<mysqldatabase>longjingdb</mysqldatabase>
    	<mysqltable>mac</mysqltable>
    	<mysqlstbtype>L6000</mysqlstbtype>
    	<irdetokeytype>1</irdetokeytype>
    	<printerhost>192.168.1.51</printerhost>
    	<printerport>4001</printerport>
    </root>

编写了一个类来解析，用一个字典来存放

	class SYSXMLParser(object):
    def __init__(self,file):
        self.xmlFile = file
        self.sysXMLDict = {}

    def getSysXMLDict(self):
        tree = ET.parse(self.xmlFile)
        root = tree.getroot()

        for child in root.getchildren():
            self.sysXMLDict[child.tag] = child.attrib
            self.sysXMLDict[child.tag] = child.text

        return self.sysXMLDict

执行后打印的结果如下

	{'mysqlstbtype': 'L6000', 'mysqlpassword': '123456', 'version': '1.0.04', 'printerhost': '192.168.1.51', 'printerport': '4001', 'mysqltable': 'mac', 'mysqldatabase': 'longjingdb', 'mysqlport': '3306', 'mysqluser': 'root', 'mysqlhost': '10.10.10.240', 'irdetokeytype': '1'}

#### 创建XML
手头上刚好有个实例，提供一个文件夹，里面是一些烧录镜像文件，针对这个镜像文件夹，需要生成一个XML文件，XML指定各个镜像文件的名字、对应烧录的地址、还有镜像的路径和md5值。

	# -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	import os
	import sys
	import xml.etree.ElementTree as ET

	from common.constant import *
	from checksum.md5 import *

	FirstRoundImages = {'pmp.toc':PMP_ADDRESS,'secboot.toc':SECBOOT_ADDRESS,'secos.toc':SECOS_ADDRESS,'secosbak.toc':SECOS_BACK_ADDRESS,
                    'u-boot.toc':UBOOT_ADDRESS,'u-bootbak.toc':UBOOT_BACK_ADDRESS,'splash.dat':SPLASH_ADDRESS}

	SecondRoundImages = {'factorytest.img':FACTORYTEST_ADDRESS,'boot.img':BOOT_ADDRESS,'system.img':SYSTEM_ADDRESS,'dvbdata.img':DVBDATA_ADDRESS,
                     'userdata.img':USERDATA_ADDRESS,'cache.img':CACHE_ADDRESS,'otaloader.img':OTALOADER_ADDRESS,'iploader.img':IPLOADER_ADDRESS,
                     'recovery.img':RECOVERY_ADDRESS}

	class GenerateConfigXML(object):
    	firstRoundImageDict = {}
    	secondRoundImageDict = {}

    	def __init__(self,path):
        	self.path = path

    	def buildConfigXML(self):
        	'''
			:param path: images dir
			:return:
			'''

        	self.listDir(self.path)

        	root = ET.Element("root")

        	self.firstRound = ET.SubElement(root,"FirstRound")
        	for image in self.firstRoundImageDict.keys():
            	if image == "pmp.toc":
                	imagePmp = ET.SubElement(self.firstRound,image)
                	imagePmp.set("name",image)
                	imagePmp.set("address",self.firstRoundImageDict[image])
                	imagePmp.set("path",os.path.relpath(self.path + "/" + image))
                	imagePmp.set("md5",CalcMD5.calcFileMd5(self.path + "/" + image))
                	self.firstRoundImageDict.pop(image)
                	break

        	for image in self.firstRoundImageDict.keys():
            	if image == "secboot.toc":
                	imagePmp = ET.SubElement(self.firstRound,image)
                	imagePmp.set("name",image)
                	imagePmp.set("address",self.firstRoundImageDict[image])
                	imagePmp.set("path",os.path.relpath(self.path + "/" + image))
                	imagePmp.set("md5",CalcMD5.calcFileMd5(self.path + "/" + image))
                	self.firstRoundImageDict.pop(image)
                	break

        	for image in self.firstRoundImageDict.keys():
            	if image == "secos.toc":
                	imagePmp = ET.SubElement(self.firstRound,image)
                	imagePmp.set("name",image)
                	imagePmp.set("address",self.firstRoundImageDict[image])
                	imagePmp.set("path",os.path.relpath(self.path + "/" + image))
                	imagePmp.set("md5",CalcMD5.calcFileMd5(self.path + "/" + image))
                	self.firstRoundImageDict.pop(image)
                	break

        	for image in self.firstRoundImageDict.keys():
            	if image == "secosbak.toc":
                	imagePmp = ET.SubElement(self.firstRound,image)
                	imagePmp.set("name",image)
                	imagePmp.set("address",self.firstRoundImageDict[image])
                	imagePmp.set("path",os.path.relpath(self.path + "/" + 'secos.toc'))
                	imagePmp.set("md5",CalcMD5.calcFileMd5(self.path + "/" + 'secos.toc'))
                	self.firstRoundImageDict.pop(image)
                	break


        	for (name,address) in self.firstRoundImageDict.items():
            	if name == "pmp.toc":
                	continue

            	if name == "u-bootbak.toc":
                	imageName = ET.SubElement(self.firstRound,name)
                	imageName.set("name",name)
                	imageName.set("address",address)
                	imageName.set("path",os.path.relpath(self.path + "/u-boot.toc"))
                	imageName.set("md5",CalcMD5.calcFileMd5(self.path + "/u-boot.toc"))
                	continue

            	imageName = ET.SubElement(self.firstRound,name)
            	imageName.set("name",name)
            	imageName.set("address",address)
            	imageName.set("path",os.path.relpath(self.path + "/" + name))
            	imageName.set("md5",CalcMD5.calcFileMd5(self.path + "/" + name))

        	self.secondRound = ET.SubElement(root,"SecondRound")
        	for (name,address) in self.secondRoundImageDict.items():
            	imageName = ET.SubElement(self.secondRound,name)
            	imageName.set("name",name)
            	imageName.set("address",address)
            	imageName.set("path",os.path.relpath(self.path + "/" + name))
            	imageName.set("md5",CalcMD5.calcFileMd5(self.path + "/" + name))

        	tree = ET.ElementTree(root)
        	self.indent(root)

        	if os.path.exists(XML_CONFIG_FILE):
            	os.remove(XML_CONFIG_FILE)

        	tree.write("config.xml")


    	def listDir(self, path):
        	for root,dirs,files in os.walk(path):
            	for file in files:
                	if file in FirstRoundImages.keys():
                    	print("firstRound: " + file)
                    	if file == 'secos.toc':
                        	self.firstRoundImageDict[file] = FirstRoundImages[file]
                        	self.firstRoundImageDict['secosbak.toc'] = FirstRoundImages['secosbak.toc']
                        	continue

                    	if file == 'u-boot.toc':
                        	self.firstRoundImageDict[file] = FirstRoundImages[file]
                        	self.firstRoundImageDict['u-bootbak.toc'] = FirstRoundImages['u-bootbak.toc']
                        	continue

                    	self.firstRoundImageDict[file] = FirstRoundImages[file]

                	if file in SecondRoundImages.keys():
                    	print("secondRound: " + file)
                    	self.secondRoundImageDict[file] = SecondRoundImages[file]



    	def indent(self, elem, level=0):
        	i = "\n" + level*"  "
        	if len(elem):
            	if not elem.text or not elem.text.strip():
                	elem.text = i + "  "
            	for e in elem:
                	self.indent(e, level+1)
            	if not e.tail or not e.tail.strip():
                	e.tail = i
        	if level and (not elem.tail or not elem.tail.strip()):
            	elem.tail = i

        	return elem

	if __name__ == '__main__':
    	if len(sys.argv) < 2:
        	print("Usage: python3 generateConfigXml.py dirOfTheImages")
        	sys.exit(1)

    	obj = GenerateConfigXML(sys.argv[1])
    	obj.buildConfigXML()

最后生成的config.xml内容如下：

	<root>
  	<FirstRound />
  	<SecondRound>
    	<factorytest.img address="otaloaderbak" md5="2bca7c24acf471ad4126e63224b117c3" name="factorytest.img" path="factory20160411/factorytest.img" />
    	<iploader.img address="iploader" md5="40d6bf4fe05bb4ce2d500464d48da5a4" name="iploader.img" path="factory20160411/iploader.img" />
    	<boot.img address="boot" md5="b1937b921ee1122f1946dcb96811e69e" name="boot.img" path="factory20160411/boot.img" />
    	<system.img address="system" md5="12fe7f8c8920c18a3cc9a815f201cca7" name="system.img" path="factory20160411/system.img" />
    	<otaloader.img address="otaloader" md5="0831e96caf91482dbb9efde1ca29d3bd" name="otaloader.img" path="factory20160411/otaloader.img" />
    	<userdata.img address="userdata" md5="536d084f75a46470f2373e3052d288dc" name="userdata.img" path="factory20160411/userdata.img" />
  	</SecondRound>
	</root>

最后顺便提供下python3下的MD5计算方法，见下面这个类

	# -*- coding: utf-8 -*-
	__author__ = 'djstava@gmail.com'

	import hashlib

    class CalcMD5(object):
    def __init__(self):
        pass

    @classmethod
    def calcFileMd5(self,filePath):
        '''
        :param filePath:
        :return: file checksum value
        '''

        md5 = hashlib.md5()

        fp = open(filePath,'rb')
        md5.update(fp.read())

        while True:
            block = fp.read(1048576)
            if not block:
                break
            md5.update(block)

        fp.close()
        return md5.hexdigest()

    @classmethod
    def calcStringMd5(self,str):
        '''
        :param str:
        :return: string checksum value
        '''

        return hashlib.md5(str.encode("utf-8")).hexdigest()
