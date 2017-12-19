### 软硬件环境

- macOS Sierra
- Xcode 8.2.1

### 前言

前面学习iOS编程时，画UI控件的时候用的都是直接敲代码的方式，基本都是先创建一个相应控件的对象，然后设置对象属性，最后添加相应的事件响应方法。那强大如Apple这样的公司有没有提供一种更方便、更易学的UI编写方法呢？答案是肯定的。那就是今天本文的主角XIB。它是一种可视化的方法，极大地降低了学习iOS编程的门槛。

### XIB实践

创建一个名叫iOSXIBDemo的工程，打开工程info.plist文件，将Main storyboard file base name这个标签去掉，如下图所示

![iOS_xib_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_1.png)

创建一个iOS的Cocoa Touch类RootViewController，作为根视图控制器，它继承自UIViewController，并勾选上"Also create XIB file"选项

![iOS_xib_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_2.png)

![iOS_xib_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_3.png)

打开RootViewController.xib文件，从控件区中拖动一个UILabel和一个UIButton，界面上的操作就是这么简单，那XIB怎么跟代码同步呢？别着急，找到Xcode右上角的双环标记，单击它

![iOS_xib_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_5.png)

![iOS_xib_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_6.png)

这样在同一个画面就能同时看到XIB和RootViewController.m了，接着开始进行代码同步。找到XIB中的UILable对象，同时按住Ctrl键 + 鼠标左键，将光标移动到RootViewController.m中的@interface和@end之间放开，弹出对话框。这里Connection的类型，对于UILabel来说是选Outlet，而UIButton选的是Action。我的理解是的可以响应事件的控件就选Action

![iOS_xib_07](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_7.png)

![iOS_xib_08](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_8.png)

绑定完成后，在对应的代码前会有个实心圆。在UIButton点击响应时间函数加句打印

```
- (IBAction)mButton:(id)sender {
    NSLog(@"button clicked.") ;
}
```

接下来开始加载根视图控制器，修改AppDelegate.m中的didFinishLaunchingWithOptions方法，同时需要将RootViewController.h头文件import进来

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds] ;
    
    RootViewController* root = [[RootViewController alloc] initWithNibName:@"RootViewController" bundle:[NSBundle mainBundle]] ;
    
    self.window.rootViewController = root;
    
    [self.window makeKeyAndVisible] ;
    
    return YES;
}
```

最后代码执行的效果如下

![iOS_xib_09](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_9_9.png)

### 源码下载

https://github.com/djstava/iOSLearning/tree/master/iOSXIBDemo