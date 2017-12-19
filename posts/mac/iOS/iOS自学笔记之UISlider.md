### 软硬件环境

- macOS Sierra
- Xcode 7.0.1

### 简介

UISlider即滑动条，经常用来表示某种状态的变化，比如说最常见的音量的调节、亮度的调节等等，是iOS中的基础控件，本文就是滑动条的简单使用。

### 动手实践

创建一个iOS工程iOSSlideDemo，在ViewController.m文件中输入

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    //创建一个UISlider，并指定其位置
    UISlider *slider = [[UISlider alloc] initWithFrame:CGRectMake(100, 100, 100, 40)];
    
    //背景颜色
    slider.backgroundColor = [UIColor yellowColor];
    
    //UISlider滑过的颜色
    slider.tintColor = [UIColor blueColor];
    
    //UISlider上点的颜色
    slider.thumbTintColor = [UIColor redColor];
    
    //UISlider响应事件
    [slider addTarget:self action:@selector(sliderPress:) forControlEvents:UIControlEventValueChanged ];
    
    //UISlider的最小值
    slider.minimumValue = 0;
    
    //UISlider的最大值
    slider.maximumValue = 100;
    
    //为YES时，能持续看到UISlider的变化，
    //为NO时，只能在UISlider停止时看到变化
    //本例中，当continuous为YES时，控制台会一直打印当前UISlider的值，而为NO时，滑动过程中不打印值，当滑动停止时才打印
    slider.continuous = NO;
    
    [self.view addSubview:slider];
}

//事件响应方法
- (void)sliderPress:(id)sender{
    NSLog(@"%f",((UISlider*)sender).value);
}
```

代码运行结果如下

![uislider_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_5_1.png)

### 源码下载

https://github.com/djstava/iOSLearning/tree/master/iOSSlideDemo