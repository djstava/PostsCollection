### 软硬件环境

- macOS Sierra
- java 1.8.0_65
- maven 3.5.0
- idea 2017.1.5

### 前言

最近接触了一点java web相关的知识，了解一下最近比较火的开发框架Spring Boot，站在一个从未涉足过java web和spring的开发者角度来讲，spring boot确实是一个非常不错的框架，配置简单，容易入门，对于想入行java web的童鞋，是一个很好的切入点。

### maven安装

这里选择maven作为构建工具，你也可以使用其它的，如gradle等。到apache站点<https://maven.apache.org/download.cgi>下载最新稳定版的zip包，解压到指定目录，比如我这里是/Users/djstava/Workshop/tools，然后编辑/Users/djstava/.bash_profile文件，在文件末尾增加语句

```
export PATH=$PATH:/Users/djstava/Workshop/tools/apache-maven-3.5.0/bin
```

设置完成后，在Terminal中执行

```
source ~/.bash_profile
```

上述操作后，最后来检查一下设置的结果是否正确，在终端中执行

```
mvn -version
```

### IDE选择

我这里选择jetbrains的[IntelliJ IDEA](http://www.jetbrains.com/idea/?fromMenu)，当然你也可以选择其它的，如eclipse。

### 创建第一个应用

##### 新建工程

点击Create New Project

![sb_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_01.png)

选择左侧的Spring Initializer,Project SDK选择java 1.8

![sb_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_02.png)

接下来是一些工程基本信息的填写，Type选择Maven Project，Packaging选择Jar,其它的可以随意写

![sb_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_03.png)

依赖项选择Web，如图所示

![sb_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_04.png)



![sb_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_05.png)

工程创建成功后，第一个构建过程会比较慢，等待完成后，点击DemoApplication.java中的main函数前的绿色按钮就可以启动工程了

![sb_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_06.png)

这时候打开浏览器，输入localhost:8080查看

![sb_07](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_07.png)

上面浏览器输出是正常的。接下来我们加入点代码，让它能够显示点东西出来。创建一个新的java类

![sb_08](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_08.png)

##### HelloController.java源码

```
package com.xugaoxiang;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by djstava on 15/07/2017.
 */
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return "Hello Spring Boot!";
    }

}
```

重新启动工程，查看浏览器,输入地址localhost:8080/hello

![sb_09](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/springboot_09.png)

### 工程解释

@RestController和@RequestMapping，这是2个很重要的注解。@RestController负责处理http请求，@RequestMapping负责url的映射。