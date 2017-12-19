###vim操作

##### 基本操作
命令              |            描述
:-----------------------|:---------------------
:set number |  加行号
:w | 保存文件
:wq | 保存并退出
:q | 退出vim
:q! | 不保存退出
:x | 保存退出
:sav 文件名 | 保存成文件名
:i | 进入插入(编辑)状态
按Esc键 | 退出编辑状态

##### 语法高亮
命令              |            描述
:-----------------------|:---------------------
:syntax on | 打开语法高亮
:syntax off | 关闭语法高亮
:set syntax=perl | 强制语法高亮

##### 文件内移动
命令              |            描述
:-----------------------|:---------------------
h | 左移
l | 右移
j | 下移
k | 上移
e | 移到单词末尾
b | 移到单词开头
0 | (数字0)移到行首
^ | 移动到行首
I | (大写字母i)光标移到行首，并进入编辑状态
o | (字母o)光标移到下一行行首，并进入编辑状态
O | (大写字母o)光标移到上一行行首，并进入编辑状态
$ | 光标移到行末尾
A | 光标移到行末尾，并进入编辑状态
G | 光标移到文件的最后一行的起始位置
gg | 光标移到文件开头
L | 光标移到当前屏幕的底部
:行数 | 移动光标到指定行数
列数l | (列数+字母l)移动光标到指定列
{ | 光标移动到上段
} | 光标移动到下段
( | 光标移动到上句
) | 光标移动到下句

##### 复制、剪切、粘贴
命令              |            描述
:-----------------------|:---------------------
y | 拷贝选中的文本
p | (小写字母p)粘贴到光标后
P | (大写字母p)粘贴到光标前
x | 剪切当前字符
s | 剪切当前字符并进入编辑状态
dd | 剪切当前行
5dd | 剪切自当前行起的5行内容
yy | 复制当前行
5yy | 复制自当前行起的5行内容
y$ | 复制当前到文件尾的内容
D | 剪切当前到文件尾的内容
dG | 删除自光标起的所有行
u | 回到修改前的状态
cw | 删除光标所在的单词，如果光标在单词的中间，则删除自光标处到单词结尾，并进入编辑状态

##### 查找
命令              |            描述
:-----------------------|:---------------------
/字符串 | 从头开始查找字符串，按n跳到下一处，N跳到上一处
?字符串 | 从尾开始查找字符串
* | 查找光标所在的单词
/\c字符串 | 查找字符串，不分大小写
/l[io]ve | 查找love或者live
/\<字符串 | 查找以指定字符串开头的单词
/字符串\> | 查找以指定字符串结尾的单词
/\<字符串\> | 查找完全匹配字符串的单词

##### 替换
命令              |            描述
:-----------------------|:---------------------
r | 光标移动到要替换的字符，按下r再输入另一字符进行替换
:%s/旧字符串/新字符串/g | 将文件中的旧字符串替换成新字符串
:起始行,结束行s/旧/新/g | 将起始行到结束行内的旧字符串替换成新字符串
:%s/*$//g | 删除所有空格
:g/字符串/d | 删除包含字符串的所有行
:%s/^/字符串/g | 在每行开头插入字符串
:%s/$/字符串/g | 在每行末尾插入字符串
:s/旧/新/ | 在当前行把第一次出现的旧字符串替换成新字符串
:s/旧/新/g | 在当前行把出现的旧字符串替换成新字符串

##### vim与linux命令结合使用
命令              |            描述
:-----------------------|:---------------------
:!命令 | 执行命令
!!命令 | 执行命令并将输出添加到当前文件当前位置，会替换原来的内容
:r !命令 | 执行命令并将输出添加到当前文件当前位置的下一行
:sh | 临时回到命令行，原来打开文件的进程在后台运行

##### 文本缩进
命令              |            描述
:-----------------------|:---------------------
:set autoindent | 打开自动缩进
:set smartindent | 打开智能缩进
:set shiftwidth=4 | 定义缩进大小为4个空格
>> | 当前行缩进
5>> | 自光标起5行整体进行缩进
<< | 当前行不缩进(un-indent)
5<< | 自光标起5行整体不缩进

##### 读写文件
命令              |            描述
:-----------------------|:---------------------
:起始行数,结束行数 w 输出文件名 | 将从起始行数到结束行数输出到指定文件名中，如果文件名已经存在，则w后要跟上!,且文件内容会被替换
:起始行数,结束行数 w >> 输出文件名 | 将从起始行数到结束行数输出到指定文件名的末尾
:r 文件名 | 将指定文件名的内容添加到当前文件中来
:行数r 文件名 | 将指定文件名的内容添加到当前文件的指定行处

##### 大小写
命令              |            描述
:-----------------------|:---------------------
Vu | 将当前行中的大写字符转换成小写字符
VU | 将当前行中的小写字符转换成大写字符
g~~ | 将当前行的大小写互换
vEU | 将单词转换成大写
ggguG | 全文转换成小写
gggUG | 全文转换成大写
:%s/\<./\u&/g | 将每个单词的首字母转换成大写
:%s/\<./\l&/g | 将每个单词的首字母转换成小写
:%s/.*/\u& | 将每行的首字母转换成大写
:%s/.*/\l& | 将每行的首字母转换成小写
gU0 | (数字0)从光标所在位置到行首，都变为大写
gU$ | 从光标所在位置到行尾，都变为大写
gUG | 从光标所在位置到文章末尾都变为大写
gU1G | 从光标所在位置到第一个字符，都变为大写

### 插件汇总
	sudo apt-get install ctags cscope
    sudo apt-get install manpages-dev manpages-posix-dev

#### Ctags
	ctags –R   生成tags文件
	:set tags=*/tags    导入tags文件
	<C-]>  跳转到函数定义处
	<C-t>  跳转到函数被调用处，从<C-]>返回
	F9:    产生tags文件

#### Cscope

	cscope –Rbq   生成cscope相关文件
	:cs add cscope.out   导入到vim中

	:cs find c|d|e|f|g|i|s|t name
	c: 查找调用本函数的函数
	d: 查找本函数调用的函数
	e: 查找本egrep模式
	f: 查找本文件
	g: 查找本定义
	i: 查找包含本文件的文件
	s: 查找本c符号
	t: 查找本字符串

#### 代码自动补全    
代码补全需要ctags的支持，在配置文件.vimrc中添加    

	filetype plugin indent on
在代码中输入变量名或是函数名的前几个字母，按ctrl+x ctrl+o，就会出现匹配选项，多个目标可以通过ctrl+n或是ctrl+p来进行选择(函数名、变量名、结构名、结构成员补全时用ctrl+x ctrl+o，需要头文件名补全时用ctrl+x ctrl+i，需要文件路径补全时用ctrl+x ctrl+f)。

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_01.png)

#### cvim
下载地址<http://www.vim.org/scripts/script.php?script_id=213>    

	cd ~/.vim（如果你的机器没有这个目标请自行创建）    
	unzip ~/Downloads/cvim.zip(firefox,chrome默认下载目录，请自行更改)

##### 插件体验    
自动添加文件头部注释  

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_02.png)

更改默认文件头部注释(所有的模板都可以进行更改，在同一目录下)    

	vi ~/.vim/c-support/templates/Templates

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_03.png)

添加函数快捷键\if（main函数用的是\im）    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_04.png)

添加预定义代码片断快捷键\nr    
预定义的代码文件存放在~/.vim/c-support/codesnippets下，例如想要在文件中添加代码片断calloc_double_matrix.c，可以输入\nr calloc_double_matrix.c(这里也可用tab键补全)。经测试，将自己编写的一段代码放codesnippets下，用同样方法添加也是可行的。

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_05.png)    

文本块注释快捷键\cc，去注释\co    

保存并编译文件快捷键\rc，执行快捷键\rr    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_06.png)    

#### man.vim
在浏览代码中，碰到系统函数调用，可能对于它们的参数类型、参数个数、返回值不确定，这时man.vim就被派上用场了
##### 使能man.vim插件

    vi ~/.vimrc
	:source $VIMRUNTIME/ftplugin/man.vim   
man.vim插件自vim包自带的，存放在/usr/share/vim/vim72/syntax/man.vim和/usr/share/vim/vim72/ftplugin/man.vim，当然你也可以将其拷贝到~/.vim/ftplugin下统一管理，source的时候更改相应的路径即可。    

在vim命令模式下:Man 系统调用(如:Man strcpy)   

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_07.png)  

如果说仅仅是需要显示man帮助信息，在vim里将光标移动到相应系统调用处按下shift+k即可显示，这种方法的不足之处就是man帮助信息会覆盖原来的代码，而man.vim是将帮助信息和原来的代码分隔显示的。

#### echofunc.vim
下载地址<http://www.vim.org/scripts/script.php?script_id=1735>    
使用方法    

	1 将下载的echofunc.vim放到~/.vim/plugin/
    2 编辑~/.vimrc，加入nmap <F9> <Esc>:!ctags -R --fields=+lS *<CR>
    3 打开项目中的某一文件，按下F9产生tags文件，再导入tags(set tags=tags)，当输入某一函数名再加(时，屏幕底端就会出现该函数的原型。

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_08.png)

echofunc插件对库函数不起作用，如果想在输入库函数时也能显示函数原型，可以使用如下笨办法

    1 cd /usr/include
    2 sudo ctags -R --fields=+lS
    3 在.vimrc中加入set tags=/usr/include/tags

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_09.png)

#### textmateonly
在代码编写时，经常会有一些固定格式的语句块，如for、if、do while，如果有办法能将其整个结构列出，实现自动添加，就能大大提升编写效率，幸运的是textmateonly就能帮助我们解决这些问题。    

    cd ~/.vim/
    svn co http://snippersemu.googlecode.com/svn/branches/textmateOnly
    vi ~/.vimrc

    filetype on
	filetype plugin on
	set runtimepath+=~/.vim/textmateOnly
	set runtimepath+=~/.vim/textmateOnly/after

也许你和我一样，觉得默认的格式过于丑陋，你可以自己去修改结构模板，路径在~/.vim/textmateOnly/after/ftplugin，我将c_snippets.vim中if和for模块修改如下，其它模板视你自己喜好修改    

	exec "Snippet if if(".st.et.")<CR>{<CR>""<CR>}<CR>"""
	exec "Snippet for for(".st.et."; ;)<CR>{<CR>""<CR>}"""

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_10.png)

#### Vundle管理vim插件
Vim用得久了，插件也越来越多，如何才能有效地管理这些插件，快速搭建/恢复自己熟悉的工作环境，是每一个使用vim的人应该思考的问题，vundle的出现使得管理这些插件变得非常方便快捷，赶紧来试试吧。

	git clone http://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

    vi ~/.vimrc

    " For vundle
	set nocompatible
	filetype off

	set rtp+=~/.vim/bundle/vundle/
	call vundle#rc()
	Bundle 'gmarik/vundle'

	" vim-scripts repos
	Bundle 'bash-support.vim'
	Bundle 'perl-support.vim'
	filetype plugin indent on "
其中语句Bundle后的插件名称来自于<http://vim-scripts.org/vim/scripts.html>的Name列    

##### vundle测试
打开vim在命令模式下键入:BundleInstall开始下载安装，从log里可以看出是git clone *.git到~/.vim/下的    

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/vim/vim%E6%B1%87%E6%80%BB_11.png)

其它命令    

	更新插件  :BundleInstall!
    缷载插件  :BundleClean


### 参考文献
1、<http://cscope.sourceforge.net/cscope_vim_tutorial.html>    
2、<~/.vim/hotkeys.latex/c-hotkeys.pdf>    
3、<https://github.com/djstava/vim_configuration>
