### 软硬件环境

- macOS Sierra
- java 1.8.0_65
- maven 3.5.0
- idea 2017.1.5

### 前言

前面一篇<>已经搭建好了spring boot的开发环境并且完成了第一个Hello world程序，本文紧接着上篇的内容，开始数据库相关的知识点。

### 工程相关配置

##### pom.xml

在dependencies标签下增加依赖，一个是spring data jpa，另一个是mysql

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
```

##### application.properties

在这个配置文件中需要写上mysql的驱动、服务器地址、端口、数据库名称、用户名、密码等信息。

```
spring.datasource.dbcp2.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/dbvcms
spring.datasource.username=root
spring.datasource.password=djstava
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

其中spring.jpa.hibernate.ddl-auto=update表示操作数据库时，所有的操作都是更新操作，这里还可以取值create、create-drop等值

### spring data jpa

spring data jpa操作mysql数据库非常简单，简单到什么程度？根本不需要你创建任何表以及表的各个字段。这个有点像ORM(对象关系映射)。眼见为实，下面就看看具体实现步骤。

##### 创建实体类

这里的实体类是指欲操作对象，包含它的各种属性，对应到数据中的各个字段。

LiveChannel.java

```
package com.xugaoxiang;

import org.springframework.beans.factory.annotation.Autowired;

import javax.persistence.Entity;
import javax.persistence.Id;

/**
 * Created by djstava on 10/09/2017.
 */
@Entity
public class LiveChannel {

    @Id
    @Autowired
    private Integer id;

    // 频道名称中文
    private String name_chn;

    // 频道名称英文
    private String name_eng;

    // 频道url
    private String url;

    // 频道是否需要播放广告
    private Boolean hasAds;

    public String getName_chn() {
        return name_chn;
    }

    public void setName_chn(String name_chn) {
        this.name_chn = name_chn;
    }

    public String getName_eng() {
        return name_eng;
    }

    public void setName_eng(String name_eng) {
        this.name_eng = name_eng;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public Boolean getHasAds() {
        return hasAds;
    }

    public void setHasAds(Boolean hasAds) {
        this.hasAds = hasAds;
    }
}
```

上面实体类包含直播频道的一些信息。

##### 接口实现

创建LiveChannelRepository.java,继承自JpaRepository,其中的Interger表示的是id的数据类型

```
package com.xugaoxiang;

import org.springframework.data.jpa.repository.JpaRepository;

/**
 * Created by djstava on 10/09/2017.
 */
public interface LiveChannelRepository extends JpaRepository<LiveChannel,Integer> {
}
```

##### 操作数据库

创建LiveController,这里设计了一个RestfulAPI，/live，返回的是数据库中所以的直播列表。

```
package com.xugaoxiang;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * Created by djstava on 10/09/2017.
 */
@RestController
public class LiveController {

    public LiveController() {

    }

    @Autowired
    private LiveChannelRepository liveChannelRepository;


    @GetMapping(value = "/live")
    public List<LiveChannel> getLiveChannel() {
        return liveChannelRepository.findAll();
    }

}
```

### 创建数据库

如上配置信息所示，创建数据库dbvcms

![jpa_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/jpa_01.png)

这里我们是没有去创建表结构的，启动工程的话，jpa会自动帮你把表创建好，各个字段的信息就来自于文件LiveChannel.java

为了方便查询，我们添加2条记录，如下所示

![jpa_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/jpa_02.png)

### 测试

一切准备就绪，启动项目，在浏览器中输入

```
http://localhost:8080/live
```

![jpa_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/web/jpa_03.png)