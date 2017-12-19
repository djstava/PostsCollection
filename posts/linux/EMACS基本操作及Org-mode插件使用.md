### 软硬件环境
* Ubuntu kylin

本文第一部分整理了一些EMACS的基础操作，入门专用，接着是插件org-mode的安装、配置和使用方法。

### EMACS基本操作快捷键
#### 关闭EMACS    
C-x C-c   退出    
C-z   暂时退出，即suspend，可用fg唤起

#### 浏览文本    
C-n   下一行    
C-p   上一行    
C-b   回退一个字符    
C-f   前进一个字符    
M-b   回退一个单词    
M-f   前进一个单词    
C-u 5 C-f   往后移动5个字符    
C-u 5 C-v   往上移动8行    
C-v   显示下一屏     
M-v   显示上一屏    
C-a   光标移到行首    
C-e   光标移到行尾    
M-a   光标移到句首    
M-e   光标移到句尾    
C-l   屏顶/屏中/屏底显示    

#### 插入删除    
C-d   删除光标所在字符    
M-Backspace   删除光标前的单词    
M-d   删除光标后的单词    
C-k   删除当前行光标后的所有字符    
M-k   删除当前句子光标后的所有字符    
C-SPACE C-n C-w   选中文本，进行区域删除，C-n可被替换C-b/C-f/C-p    
EMACS中有两个概念，delete和kill，killed的数据是可以被yank回来的，上面的一些操作对应于delete的是C-d、M-Backspace、M-d，C-k、M-k则对应kill    
C-y   就是yank操作了，我的理解就是粘贴。如果做了多次的类似C-k操作，则C-y就是被kill掉的所有内容    
C-u 5 *   输入5个*    
C-g   取消当前操作，如想输入5个＊，做到一半不想做了，取消谅用这个命令    
C-x u   取消上一操作（该操作已经被执行了）    

#### 查找替换    
C-s   等待输入进行查找，再C-s查找下一个，C-r查找上一个    
M-x replace-string   替换操作    

#### 文件操作    
C-x C-f   find一个文件，回车后打开，原来打开的文件还存在于EMACS中，此时在EMACS窗口下方的区域称为minibuffer   

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C_01.png)

C-x C-s   保存文件    
这里又有个概念，buffer：EMACS stores each file's text inside an object.理解上可认为文件，不过它包括的范围更广些。    
C-x C-b   列出buffers    
C-x b BUFFER_NAME   打开某个buffer    
C-x s   保存某个打开的buffer    

#### 多窗口    
C-x 2   打开2个窗口    
C-M v   光标不在的窗口的滚屏操作，下一屏（只包含2个窗口的frame中）    
SHIFT-C-M v   光标不在的窗口的滚屏操作，上一屏（同上）    
C-x 1   关闭除光标所在的所有窗口    
C-x o   切换光标（小写字母o）    
C-x 4 C-f   寻找文件，若打开则在新窗口中打开    

#### frame    
frame相当于一个EMACS的实例    
M-x make-frame   打开一个新frame    
M-x delete-frame   delete选中的frame    

### Org-mode插件
Org-mode是Emacs的一种个人信息管理和大纲化工具。本文讲解其基本使用方法，如有兴趣请访问org-mode官网。

#### 获取org-mode    
<http://orgmode.org/index.html>

#### 安装配置    
为方便管理Emacs诸多插件，在~/.emacs.d/新建一个目录plugins，将org-mode解压到此。另外，为方便管理org文件，创建一文件夹保存。    

	cd org-7.3    
    make    

在~/.emacs文件中加入如下语句    

    ;;;org-mode;;;
    (setq load-path (cons "~/.emacs.d/plugins/org-7.3/lisp" load-path))
    (setq load-path (cons "~/.emacs.d/plugins/org-7.3/contrib/lisp" load-path))

    (require 'org-install)
    (require 'org)
    (require 'org-publish)
    (add-to-list 'auto-mode-alist '("\\.org\\'" . org-mode))
    (add-hook 'org-mode-hook 'turn-on-font-lock)
    (add-hook 'org-mode-hook
    (lambda () (setq truncate-lines nil)))

    (global-set-key "\C-cl" 'org-store-link)
    (global-set-key "\C-ca" 'org-agenda)
    (global-set-key "\C-cb" 'org-iswitchb)
    (setq org-log-done t)

#### 基本操作
如果顺利的话，此时emacs打开扩展名为org的文件时，emacs将进入到org-mode。我新建了一个tutorial.org文件，打开后即进入了org-mode。   

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_01.png)

一级标题以一个星号开头；二级标题以两个星号开头。其他标题以此类推。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_02.png)

将光标移到TODO这一行，执行C-c C-t可将TODO标记为DONE,再执行一次则由DONE变为TODO。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_03.png)

现在再添加几条TODO，执行M-shift Enter。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_04.png)

将想法大纲化，即整理出各级标题，然后添加内容，就非常方便了。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_05.png)

接下来是添加文件内容了，大概地分为文本、图片（附件）和超链接。    
文本很简单，想在哪加就在哪加，移动光标就OK了。    
插入超链接: [[地址][描述性文字]]    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_06.png)

上图中是没有匹配]所导致的，完整的如下图。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_07.png)

C-c C-o即可打开此链接。    
插入外部文件如（图片，文本文件）方法： [[file:filename]]    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_08.png)

C-c C-o打开此链接如下    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_09.png)

插入文本也一样    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_10.png)

shift+TAB: 文档折叠    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_11.png)

再执行一次则得到    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_12.png)

再执行则是    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_13.png)

依次循环。    
经折叠后的标题，按TAB可展开。    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_14.png)

C-c C-s: 设置schedule时间    
C-c C-d: 设置deadline时间    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_15.png)

C-c a: agenda模式    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_16.png)

M+Enter: 现加一个同层级的标题    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_17.png)

M+Left/Right: 将当前的标题提升/降低层级，即2个星变为1个星或者3个星    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_18.png)

M+Up/Down: 将当前标题在同级标题中进行上下移动    
![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/emacs/EMACS_org_mode_19.png)
