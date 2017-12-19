### 软硬件环境
* Xcode 7

### 前言
众所周知,今天的苹果公司已经成为全球市值最高的科技公司,苹果旗下有非常多的产品,如我们都非常熟悉的iphone系列、ipad系列、macbook系列、apple watch等等都是同类产品中的佼佼者。受益于苹果各大系统的封闭性,苹果公司能够完全掌控其产品的发展方向,包括硬件、软件及设计,一直以来,苹果都被视为以硬件及设计为主的公司。不过随着iphone的横空出世,apple开始构建自己的生态,到今日将近10年的时间,已经形成了自己的规模。如今,你要问为什么去买apple的产品?相信很多人的答案会是系统好用。这就是apple成功的地方。

### 和Android的比较
说到iOS就不能不提Android,同为移动应用开发的主流平台,如今Android的市场份额远远超过iOS。一个开源、一个闭源,孰优孰劣,自己体会。Android开发使用的是java语言,iOS则使用apple自己的objective-c语言,相比较而言objectvie-c接触的童鞋更少,而java使用的人更多,很多做android app的也是从java后端转过来的,因此从数量上将android的开发者会多于iOS的开发者。从每月的编程语言排名来看,java基本上是前三的位置,而objective-c则排名10名开外。

### Objective-c语言
从语言名字来看,objective-c应该会和C语言有点关系,没错,Objective-C(之后简称OC)是基于C语言的,有过C语言学习经历的童鞋都知道C语言是面向过程的编程语言,而OC除了有C语言的特性,更为重要的是它还有面向对象的特性,所以

    C + OO = OC

这里的OO表示的是Object Oriented,即面向对象

### Xcode
Xcode是apple提供的集成开发环境,它是开发所有apple系app的官方IDE,当然开发工具也有其它选择,如比较有名的appcode,出自牛X公司jetbrains。一般来说,第三方的工具对apple新特性的支持都会有一个滞后性。所以还是推荐使用官方的Xcode。

![iOS_1_1](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_1.png)

上图是Xcode启动后的画面,你可以进入playground、新建新的xcode工程、从代码仓库检出代码。playground是跟随swift语言一起出来的所见即所得的工具,跟OC没多大关系。

我们选择 Create a new Xcode Project

![iOS_1_2](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_2.png)

到这里,可以看到左侧栏有几个大类,包括iOS、watchOS、OS X、Other,对应的在右侧栏会有相应的工程模板,我们选择iOS --> Single View Application -->Next

![iOS_1_3](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_3.png)

这里填写工程的名称,组织的名称、ID、编程语言、目标设备等等信息,填写完毕后选择本地目录进行保存。

![iOS_1_4](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_4.png)

到这里,我们的第一个Xcode工程就创建好了,先什么都不做,我们编译运行一下这个工程,Product-->run 或者直接按下快捷键Command+R

![iOS_1_5](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_5.png)

模拟器上只显示一片空白。基于学习一门编程语言从hello world开始的原则,我们修改一下代码,也来玩个hello world。

从左侧文件导航栏中找到ViewController.m文件,在方法viewDidLoad里添加语句

    NSLog(@"Hello world!");

重新运行下程序

![iOS_1_6](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_1_6.png)

至此,一个完整iOS工程就完成了,虽然程序里还没有做有意义的事情,本文的目的也是介绍iOS app的基本开发流程,其它的内容我们后续再深入学习。

