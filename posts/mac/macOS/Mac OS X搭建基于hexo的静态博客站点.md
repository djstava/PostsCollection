### 软硬件环境
* OS X EI Capitan
* node.js 0.10.35
* npm 2.1.14

### 前言
hexo是一个非常强大且轻量的博客框架，基于node.js(这玩意儿我也不懂)，最早是由台湾的一名在校生Tommy Chen开发，目前代码托管在github上，感兴趣的朋友可以去看看。

### 本地安装hexo
hexo是基于node.js的，所以第一步就是安装node.js，这里采用最简单的方法homebrew

    brew instal node(安装node.js)
    npm install hexo-cli -g
    hexo init hexoBlog(通常这里会报错，略过，后面会解决)
    cd hexoBlog
    npm install(init的时候报的错，这里解决)
    hexo server

一切就绪后，在浏览器中访问 http://localhost:4000

![mac_hexo_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_01.png)

### 更改主题
hexo默认的主题是landscape，本身是不错的，但是有可能它不是你的菜，怎么办？换呗，推荐一个主题，maupassant,下面看看如何更改主题？

    git clone https://github.com/luuman/hexo-theme-spfk.git themes/spfk

修改_config.yml，将theme的值由landscap改成spfk，然后启动服务hexo server，看看插件效果

![mac_hexo_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_02.png)

![mac_hexo_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_03.png)

### 撰写博文
博文目录是source下的_posts文件夹，之前的博文都是用markdown来编写，刚好hexo的一大特色就是支持markdown，直接拷贝过来就好了，然后在博文开始加入如下信息。

    ---
    title: DHCP那些事儿
    toc: true
    ---

![mac_hexo_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_04.png)

### 将博客托管到github
在github上新建一个代码仓库，项目名为djstava.github.io，你可以任意修改前缀djstava

回到本地hexo,编辑_config.yml文件,把部署的地址指向我们刚才创建好的仓库地址

![mac_hexo_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_05.png)

接着开始部署

    hexo generate
    npm install hexo-deployer-git --save
    hexo deploy

等到文件上传(需要github用户名和密码)完毕后，浏览器访问djstava.github.io，就可以看到你的博客了。

### 绑定域名
在source文件夹下新建文件CNAME，内容就是你的域名，但是不包括http和www

### DNS设置
由于域名是在godaddy.com上买的，直接在它们的网站上设置即可。
![mac_hexo_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/mac_hexo/hexo_06.png)

### 其它定制
基本的博客站点到这里就已经搭建好了，当然你肯定不会满意，因为像网站的名字、作者信息、背景图片等等都不是你的。余下的工作就交给你自己去细化了。

### 参考文献
1、https://hexo.io    
2、https://github.com/luuman/hexo-theme-spfk   
3、http://luuman.github.io    
4、https://hexo.io/themes/
