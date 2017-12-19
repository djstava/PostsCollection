### 软硬件环境

- macOS Sierra
- Xcode 7.0.1

### UIProgressView

UIProgressView就是进度条控件，非常常用，可以用来显示某个任务的进度，它是一个被动的控件，无法主动绑定事件。

```
    //创建一个进度条
    UIProgressView* pView = [[UIProgressView alloc] init] ;
    
    //位置
    pView.frame = CGRectMake(50, 100, 200, 40) ;
    
    //当前值，范围是 0 ~ 1
    pView.progress = 0.5 ;
    
    //进度条当前值之后的颜色
    pView.trackTintColor = [UIColor yellowColor] ;
    
    //整个进度条的背景颜色
    pView.backgroundColor = [UIColor blueColor] ;
    
    //当前进度的颜色
    pView.progressTintColor = [UIColor redColor] ;
    
    //进度条style
    pView.progressViewStyle = UIProgressViewStyleBar ;
    
    [self.view addSubview:pView] ;
```

代码执行的效果如下

![uiswitch_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_7_1.png)

### UIActivityIndicatorView

UIActivityIndicatorView是等待指示器控件

```
    //创建一个UIActivityIndicatorView对象并设置其位置
    UIActivityIndicatorView* indicator = [[UIActivityIndicatorView alloc] initWithFrame:CGRectMake(100, 300, 80, 40)] ;

    //风格类型
    indicator.activityIndicatorViewStyle = UIActivityIndicatorViewStyleGray ;

    //启动UIActivityIndicatorView
    [indicator startAnimating] ;
    
    [self.view addSubview:indicator] ;
```

代码执行的效果如下

![uiswitch_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_7_2.png)

### 源码下载

https://github.com/djstava/iOSLearning/tree/master/iOSProgressViewAndIndicator