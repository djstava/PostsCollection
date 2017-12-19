### 软硬件环境
* Mac OS X 10.9.5
* Xcode 6 beta 5

### Swift调用C函数

1. 新建工程，这里创建的是OS X的command line工程

![][image-1]

2. 在Product Name里填上工程名，这里写的是SwiftCallC，点击Next，选择本地磁盘目录进行保存。

![][image-2]

3. 给工程建立一个桥接文件，它建立起了swift和c的联系。点击File--\>New File，弹出“Choose a template for your new file”对话框，选择OS X下的source，选中Objective-C File，填上文件名(OCFile.m)，点击"Next"后会弹出"Would you like to configure an Objective-C bridging header? "，选择Yes，创建SwiftCallC-Bridging-Header.h文件

	![][image-3]

	![][image-4]

	![][image-5]

4. 给工程建立一个C语言文件。跟上述步骤3类似，只不过这里选择的是C文件，这里的文件取名为CFile.c，同时自动生成CFile.h文件

	![][image-6]

	![][image-7]

5. 开始编写代码。
	在SwiftCallC-Bridging-Header.h文件中声明C函数，这里是void callCDemo()

	![][image-8]

	在CFile.c中定义这个函数

	![][image-9]

	在main.swift中调用这个C函数

	![][image-10]

6. 编译运行

	![][image-11]

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_01.png
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_02.png
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_03.png
[image-4]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_04.png
[image-5]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_05.png
[image-6]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_06.png
[image-7]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_07.png
[image-8]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_08.png
[image-9]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_09.png
[image-10]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_10.png
[image-11]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_call_c/swift_call_c_11.png
