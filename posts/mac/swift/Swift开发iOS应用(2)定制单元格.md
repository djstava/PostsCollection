### 软硬件环境
* OS X EI Capitan
* Xcode 7.0.1

### 简介
前面一节我们实现了一个简单的列表，列表中的单元格是一串字符，在实际开发中一般不会如此简单，有可能还包括其它一些控件并且这些控件的位置还会各异，这时我们就需要对单元格进行自定义，本节我们将实现这个功能。

![][image-1]

### 实现步骤
#### UI部分
新建一个工程，工程名为CustomCellDemo，模板选择Single View Application,上一节中创建的列表是通过在ViewController里选择Table View控件，然后配置代理，设置数据源来实现的，相对来说比较麻烦。本节将用一种全新的方法来实现，那就是TableViewController

首先从控件库中拖拽TableViewController到storyboard，将默认的ViewController前面的箭头移动到TableViewController，设置成初始控制器，然后删除默认的ViewController

![][image-2]

接着将源码文件ViewController.swift也删除，删除时选择”Move to Trash”选项，如下图

![][image-3]

#### 导入图片
在左侧的文件导航栏里选中Assets.xcassets，然后从Finder里将图片拖动进来，我这里只准备了一张图片，@2x、@3x暂时没有

![][image-4]

#### 表格控制器
删除了默认的控制器，就需要再重新创建一个ViewController，如下图操作

![][image-5]

#### 关联视图和控制器
打开storyboard，选中ViewController，在右侧的Identity Inspector —\> Custom Class —\> Class里选择我们刚刚创建的CustomTableViewController

![][image-6]

#### 定制单元格
首先在storyboard里设置单元格的Identifier为”Cell”,Style选择”Custom”，

![][image-7]

然后添加1个ImageView和2个UILabel控件，并调整他们的大小及位置。

#### 创建单元格类

![][image-8]

#### 关联单元格与控制类

![][image-9]

#### 编辑CustomViewController.swift
准备2个数组来作为UILabel的数据源，图片就是之前导入进来的

	var colors = ["Red","Yellow","Green","Gray","Orange","Black","White"]
	var colorsChinese = ["红色","黄色","绿色","灰色","桔色","黑色","白色"]

重写方法tableView（tableView: UITableView,numberOfRowsInSection section: Int） —\> Int，让其返回数据的元素个数，其实就是列表的行数

	override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	        // #warning Incomplete implementation, return the number of rows
	        return colors.count
	    }

最后重写方法tableView(tableView: UITableView,cellForRowAtIndexPath indexPath: NSIndexPath) —\> UITableViewCell,这里需要返回我们自定义的单元格类，使用as进行强制转换，给单元格中的UIImageView和UILable赋值，完成单元格的自定义。

	override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
	        let cell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath) as! CustomTableViewCell

	        // Configure the cell...
	        cell.cellEnglish.text = colors[indexPath.row]
	        cell.cellChinese.text = colorsChinese[indexPath.row]
	        cell.cellImagge.image = UIImage(named: "penguin")

	        return cell
	    }

完成后，command + R运行下程序，在模拟器中查看效果

### 源码下载
[https://github.com/djstava/SwiftForiOS/tree/master/CustomCellDemo][1]

[1]:	https://github.com/djstava/SwiftForiOS/tree/master/CustomCellDemo

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_10.png
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_01.png
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_02.png
[image-4]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_06.png
[image-5]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_03.gif
[image-6]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_04.png
[image-7]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_05.png
[image-8]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_08.gif
[image-9]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch/CustomCellDemo_09.gif
