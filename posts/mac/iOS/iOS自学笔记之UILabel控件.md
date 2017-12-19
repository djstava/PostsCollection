### 软硬件环境

* Xcode 7.0.1

### 前言

UILabel是一种非常常用的控件，它用来传递直白的文本信息，一目了然，一般手机界面上的引导性的信息都由UILabel来实现。

### UILable的常用属性

创建UILabelDemo工程，打开ViewController.m文件，在viewDidLoad函数中加入代码

```
//alloc & init经典创建对象方法
UILabel *lable = [[UILabel alloc] init];

//UILabel现实的文本信息
lable.text = @"Hello iOS!";

//UILabel的位置
lable.frame = CGRectMake(100, 100, 100, 40);

//UILable的背景颜色
lable.backgroundColor = [UIColor grayColor];

//阴影的颜色
lable.shadowColor = [UIColor redColor];

//阴影的偏移
lable.shadowOffset = CGSizeMake(5, 5);

//设置字体的大小
lable.font = [UIFont systemFontOfSize:20];

//设置文本的颜色
lable.textColor = [UIColor yellowColor];

//设置文本对齐方式
lable.textAlignment = NSTextAlignmentCenter;

//如果是长文本，换行显示
lable.numberOfLines = 0;

//将视图加入到父视图中
[self.view addSubview:lable];
```

![UILabel_1](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_2_1.png)

### 工程下载

<https://github.com/djstava/iOSLearning/tree/master/UILabelDemo>