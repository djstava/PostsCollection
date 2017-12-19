### 软硬件环境
* OS X EI Capitan
* Xcode 7

### 前言
Swift是Apple公司在2014年WWDC上发布的新的程序设计语言，可以与Objective-C无缝衔接，共同运行在Mac OS X、iOS和watchOS平台。之所以Apple会推出这样一门新的程序开发语言，是因为Objective-C过于古老，开发门槛相对更高，而Swift则是集当前市面上程序设计语言的诸多优点于一身，更加简单、更加高效，对扩张和巩固Apple的生态圈大有裨益。本文基于swift2.0。

### 关于Playground
Playground是xcode的一个新功能，它能够在你敲完代码后立即显示代码执行的结果，而不需要去编译然后再打开模拟器去执行，是不是很酷？本文提到的swift语法都是在这个游乐场里进行的。

![][image-1]

### 常量和变量
常量的值一旦设定就不能改变，用关键字let来声明；变量的值可以随意改变，用关键字var来声明。

	let MAX = 127 //常量名一般全大写
	var str = "hello swift" //声明一个变量并且进行初始化，这里可以看到swift里的语句不需要用;来分隔，加上也是可以的，根据你的编码习惯而定

与其它程序设计语言不同的是，可以用unicode字符来作为常量或变量名

	let π = 3.1415926
	var 你好 = "世界"
	let ?? = "djs"

### 基本数据类型
#### 整数
和大多数编程语言一样，整形是没有小数部分的数字，也有有符号和无符号之分，在swift中，还提供了2个全局的方法,max和min,分别用来获取对应类型的最大值和最小值
	Int8.max
	UInt32.min

#### 浮点数
是带有小数部分的数字，也有Float和Double两种精度的表示方式，具体要看程序的需要
	var num: Double = 3.14159267

#### 字符和字符串
	var ch: Character = "a" //声明一个字符

字符串是有序的字符类型的集合。
	var str1: String = "hello"
	var str2: String  = "swift"
	var str3 = str1 + str2 //字符串的连接
	let cha: Character = "!"
	str2.append(cha) //在字符串的尾部添加一个字符
	print("str2 is \(str2)") //字符串的插值操作

	str2.insert("?", atIndex:str2.startIndex) //在str2的开头插入一个字符
	//还有很多内置API来操作字符串，如hasPrefix检查是否有前缀，hasSuffix()检查是否有后缀，删除字符串的系列函数removeAtIndex,removeAll等等，需要的时候请自行查阅Apple的文档
#### 布尔型
Bool类型，只能取值true和false，这点跟C语言(非0为真，0为假)有点不同
	let isRight: Bool = true

#### 数组
和大多数编程语言一样，数组是相同数据类型的一个组合
	var arr: [String] = ["red","green","blue"]

#### 元组
元组是多个值的一个组合，和数组不一样的是，元组的数据类型不要求是一样的。如果想要访问某个元素，可以通过下标去访问
	let tuple1 = ("年龄",100)
	tuple1.0

#### 可选类型
可选类型会有两种情况，要么有值，要么没有即为nil
	var num: Int? //num是一个可选类型的变量，没有初始化的情况下值是nil

如果说，确定可选类型的变量有值，可以在变量后面紧跟个!来取值
	num!

swift 2.0中提供了一个新的方法if-let来进行可选绑定
	if let constantName = someOptional {
	    statements
	} else {
	    other statements
	}

#### 字典
字典是一种存储多个相同类型的值得容器，每个元素都是一个键值对(key:value),这点很像JSON,可以通过属性keys和values来获取所有的key和value

	let dict: Dictionary = ["姓名":"高翔","性别":"男"]
	dict.keys
	dict.values

### 基本运算符
像+、-、\*、/、%、+=、-=、\*=、/=、%=、++、——、\>、\>=、!=等运算符这里就不讲了，与其它语言里的都一样，主要看看swift独特的地方
	=== //恒等于，用来判断2个对象是否是引用同一个对象实例
	!== //不恒等，同上

	a ?? b //??是空合运算符，a必须是可选类型，当a的值不为空时，表达式的值为a!，否则为b，b的类型必须要和a存储的类型一样

	1...5 //开区间运算符，包括5
	1..<5 //闭区间运算符，不包括5

### 程序控制流
#### if语句
	if condition {
	    statements
	}
if语句后面可以跟上else语句来操作不满足if条件的情况，这点和其它语言无二

#### while循环
	var index: Int = 0

	while (index < 5) {
	    print("index:+ \(index)")
	    index++
	}
#### for 和 for-in循环
	//传统的for循环
	for(index=0;index<=5;index++){
	    statements
	}
	//for-in循环
	for index in 1...5 {
	    statements
	}
#### repeat-while
repeat-while跟其它语言里的do-while循环是一样的
	repeat{
	    statements
	}while condition

#### switch-case
	switch constant{
		case value1:
			statements
		case value2,value3:
			statements
			fallthrough
		default:
			do sth
	}

如果多个case的执行语句一样，case之间用逗号隔开，注意到case语句里没有break语句用来跳出循环,而如果需要在执行玩一个case后，自动执行后面的case，可以使用fallthrough关键字

### 函数
函数是用来完成特定任务的独立的代码块，swift中是这样定义函数的
	func add(a:Int,b:Int) ->Int{
	    return a+b
	}

	let num = add(3, b: 4)

swift的函数声明，比较特殊的是函数返回值的说明，需要注意。本人也更喜欢这种方式，更接近与自然语言。

函数的参数传递有传值与穿引用2中，传值时形参是实参的一份拷贝，它的改变不会影响到实参，而穿引用改变的就是实参，来看个例子
	func swap(inout a:Int,inout b:Int){
	    let temp:Int = a
	    a = b
	    b = temp
	}

	var a:Int = 3
	var b:Int = 4

	swap(&a, b:&b)

### 闭包
闭包本质上是一个代码块，与函数的区别就是，这个代码块不需要被多次调用，如果某段代码会被多次用到，就需要用函数来表示。看看swift官方给出的例子

	let names = ["Chris","Alex","Ewa","Barry","Daniella"];
	names.sort({(s1:String,s2:String)->Bool in return s1 > s2})

闭包的函数体部分由关键字in引入，注意看是不是跟函数非常像。

### 小结
本文作为Swift From Scratch系列的开篇，简单介绍了下swift语言的一些基本语法。作为iOS的新进开发者，希望这个系列能够记录自己的学习历程，也希望能够帮助到一些人，仅此而已，文中如有错误，敬请指正，推荐下参考文献2的视屏教程。
### 参考文献
* [https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift\_Programming\_Language/index.html][1]
* [http://www.imooc.com/learn/127][2]

[1]:	https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html
[2]:	http://www.imooc.com/learn/127

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_syntax/swift_syntax_01.png "playground"
