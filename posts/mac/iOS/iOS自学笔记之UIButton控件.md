### 软硬件环境

- Xcode 7.0.1

### 前言

iOS中的按钮是UIButton，也是非常常见的控件。它可以响应用户点击事件，完成特定的业务逻辑。

### UIButton的常用属性

创建UIButtonDemo工程，打开ViewController.m文件，在viewDidLoad函数中加入代码

```
//类方法创建UIButton对象，不能用alloc和init组合来创建，这点和UILable不一样
    UIButton* button = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    button.frame = CGRectMake(100, 100, 100, 40);
    
    //设置按钮上的文本显示信息
    [button setTitle:@"我是按钮" forState:UIControlStateNormal];
    
    //按钮被按下后的文本显示信息
    [button setTitle:@"按钮被按下" forState:UIControlStateHighlighted];
    
    //设置字体大小
    button.titleLabel.font = [UIFont systemFontOfSize:20];
    
    //设置按钮风格颜色，TintColor和TitleColor同时存在是TintColor不起作用
    [button setTintColor: [UIColor orangeColor]];
    
    //设置按钮颜色
    [button setTitleColor:[UIColor redColor] forState:UIControlStateNormal];
    
    //设置按钮被按下的颜色
    [button setTitleColor:[UIColor greenColor] forState:UIControlStateHighlighted];
    
    //响应点击事件，UIControlEventTouchUpInside为点击类型，点击完成后弹开仍在按钮的范围内，与其相对应的是UIControlEventTouchUpOutside，弹开后在按钮的外面
    [button addTarget:self action:@selector(buttonPressed) forControlEvents:UIControlEventTouchUpInside];
    
    [self.view addSubview:button];
```

按钮的点击响应函数放在viewDidLoad方法外实现

```
//事件响应函数
- (void)buttonPressed {
    NSLog(@"按钮被按下！");
}
```

当完成一次按钮点击后，终端就会打印一次"按钮被按下！"。

### 图片按钮

与圆角按钮不同的是,图片按钮创建时的buttonWithType为UIButtonTypeCustom，自定义而不是上面的圆角

```
//图片按钮
    UIButton* imageButton = [UIButton buttonWithType:UIButtonTypeCustom];
    imageButton.frame = CGRectMake(100, 200, 100, 40);
    [imageButton setImage:[UIImage imageNamed:@"xiaoxin.jpg"] forState:UIControlStateNormal];
    [imageButton setImage:[UIImage imageNamed:@"xiaowanzi.jpg"] forState:UIControlStateHighlighted];

    [self.view addSubview:imageButton];
```

两张图片xiaoxin.jpg和xiaowanzi.jpg事先准备好，然后拖动到当前工程中。

![UIButton_1](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_3_1.png)

### 工程下载

<https://github.com/djstava/iOSLearning/tree/master/UIButtonDemo>