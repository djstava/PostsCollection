### 软硬件环境

* macOS Sierra
* Xcode 7.0.1

### plist文件简介

iOS开发中经常会使用到plist文件，那么plist文件到底什么呢？plist的全称是Property List即属性列表文件。本质上它是一个XML文件。做过Android开发的童鞋肯定知道SharedPreference，plist就是iOS开发中的SharedPreference。plist文件一般用于存储用户设置的信息，其存储的信息量不宜过大，大数据量可以使用数据库，这个后面会学习到。

### plist文件的读写操作

回到Xcode开发环境，我们创建一个iOS工程iOSPlistOpsDemo来具体操作plist文件，工程创建完毕，打开工程默认的info.plist文件来看看它到底长什么样子？

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_1.png)

如果你习惯了以代码的形式查看，可以右击info.plist —> Open As —> Source Code打开查看

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_2.png)

工程info.plist已经包含了一些iOS默认工程的设置，我们暂不使用它。这里新建一个plist文件djstava.plist，File —> New —> File —> iOS —> Property List，然后增加如下几条数据对

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_3.png)

```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSString *path = [[NSBundle mainBundle] pathForResource:@"djstava" ofType:@"plist"];
    NSMutableDictionary *data = [[NSMutableDictionary alloc] initWithContentsOfFile:path];
    NSLog(@"%@",data);
}
```

编译后启动模拟器，会看到如下运行结果

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_4.png)



数据读取成功后，我们再来看看怎么讲数据写入到plist文件中

```
    [data setObject:@"Shanghai" forKey:@"location"];
    
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    
    NSString *fileName = [[paths objectAtIndex:0] stringByAppendingPathComponent:@"djstava.plist"];
    
    NSLog(@"%@",fileName);
    
    [data writeToFile:fileName atomically:YES];
```

再次编译运行程序，按照打印出来的路径，找到对应的djstava.plist文件，打开查看是不是已经有数据了，如下图所示

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_5.png)

这里需要注意一点的是工程中的djstava.plist并没有改变。如果你嫌这样查看不方便，可以在数据写入后再将它读出来进行比较。

### Info.plist文件的读写

Info.plist是工程的配置文件，在每个xcode工程创建时会自动生成一个，它保存这该工程的基本配置信息，你可以增加/删除配置项。Info.plist的信息读取和上面的实例基本类似

```
    //Info.plist
    //获取文件的全路径
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"Info" ofType:@"plist"];
    
    //解析 Info.plist
    NSDictionary *dict = [NSDictionary dictionaryWithContentsOfFile:filePath];
    
    //获取当前的版本号
    NSString *version = dict[@"CFBundleShortVersionString"];
    NSLog(@"%@",version);
    
    //获取storyboard文件
    NSString *storyFile = dict[@"UIMainStoryboardFile"];
    NSLog(@"%@",storyFile);
```

![plist_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/iOS/iOS_4_6.png)

### 源码下载

https://github.com/djstava/iOSLearning/tree/master/iOSPlistOpsDemo

