### 软硬件环境
* OS X EI Capitan
* Xcode 7.0.1

### 简介
列表，可以说是控件中最重要的一个，在iOS中是UITableView，本节就来学习一下如何来实现一个列表，如下所示效果图

![效果图][image-1]

### 实现步骤
#### UI部分
新建一个工程，工程名为UITableViewDemo，模板选择Single View

![][image-2]

在Xcode的右下角控件库里选择Table View，按住拖拽到storyboard里，并将其拖大至全屏

![][image-3]

将Table View的Prototype Cells设成1，接着选中Prototype Cells，在右上角的属性试图中的Stype选择Basic，设置Identifier为Cell（可以为任意，会在后面的代码文件中使用），如图

![][image-4]

### 给列表绑定数据
经过上面的操作，已经可以看到列表了，只是每个单元格都是空的，没有任何数据

这里提供一个颜色数组，它的每个元素将会被显示到相应的单元格中

	var colors = ["Red","Yellow","Green","Gray","Orange","Black","White"]

在ViewController类中需要实现UITableViewDataSource和UITableViewDelegate两个协议，然后需要实现2个方法

	func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	        return colors.count
	    }

这里返回给定数组的大小，就是整个列表有多少行

	func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
	        let cell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath)
	        cell.textLabel?.text = colors[indexPath.row]
	        return cell
	    }

上面的”Cell”就是在storyboard里的Identifier，这个函数返回一个cell，cell上显示一串字符。dequeueReusableCellWithIdentifier方法会重复利用单元格，提高效率，节省资源。常见的像微博里的消息列表，设备的每一屏只显示几条消息，下拉刷新一次，单元格还是这几个，只不过内容被重新填充了。

最后一步，切换到storyboard中，打开View Controller Scene，将Table View与View Controller做一个绑定，按住control键，拖动Table View到View Controller上放开，选择dataSource，重复一次，选择delegate

![][image-5]

至此，列表的数据才被正确地显示了出来，可以在模拟器上查看

### 源码下载
[https://github.com/djstava/SwiftForiOS/tree/master/TableViewDemo][1]

[1]:	https://github.com/djstava/SwiftForiOS/tree/master/TableViewDemo

[image-1]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch_1/UITableView_01.png
[image-2]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch_1/UITableView_02.png
[image-3]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch_1/UITableView_03.png
[image-4]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch_1/UITableView_04.png
[image-5]:	https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/swift_from_scratch_1/UITableView_05.gif
