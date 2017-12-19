### 软硬件环境

- macOS Sierra
- go 1.8

### go语言简介

Go是Google开发的一种编译型、并发型、具有垃圾回收功能的编程语言，出自三位大牛罗伯特·格瑞史莫(Robert Griesemer)、罗勃·派克(Rob Pike)及肯·汤普逊(Ken Tompson)之手，于2009年11月正式推出。Go天生的支持并发，所以非常适合服务器端软件的开发，尤其是云计算领域。

### 安装go

下载go安装包https://golang.org/dl/，这里选择目前最新版本1.8，接着进行傻瓜式安装。

### 一点配置

```
export GOPATH=/Users/djstava/Workshop/go
export GOBIN=/Users/djstava/Workshop/go/bin
export PATH=$PATH:$GOBIN
```

为方便起见，将上述配置语句写进~/.bash_profile，然后执行

```
source ~/.bash_profile
```

设置完后，检查下是否配置成功，在terminal中输入go env

```
GOARCH="amd64"
GOBIN="/Users/djstava/Workshop/go/bin"
GOEXE=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOOS="darwin"
GOPATH="/Users/djstava/Workshop/go"
GORACE=""
GOROOT="/usr/local/go"
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
GCCGO="gccgo"
CC="clang"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/1p/w8h4jd_94c5d9pvc0r2660wr0000gn/T/go-build205680752=/tmp/go-build -gno-record-gcc-switches -fno-common"
CXX="clang++"
CGO_ENABLED="1"
PKG_CONFIG="pkg-config"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
```

这里需要说明一下，$GOPATH指定的是go工程目录，$GOBIN是go程序生成的可执行文件的路径

### go工程目录结构

在$GOPATH目录中一般有3个子目录，分别是

* src    存放源代码
* bin    编译后生成的可执行文件，上面配置中已经将该目录放到$GOBIN中了
* pkg    编译后生成的文件，比如.a文件 

### 第一个go程序

环境配好了，照例写一个hello world程序吧

```
cd $GOPATH
mkdir src bin pkg
cd src
mkdir hello
cd hello
vi main.go
```

这里用vi编辑器进行编辑，在main.go文件中敲入如下代码

```
package main

import (
    "fmt"
)

func main() {
    fmt.Println("hello world!")
}
```

编写完毕后，直接在hello文件夹下编译安装

```
go install
```

生成的可执行文件hello，存放在$GOPATH/bin下

### 代码解析

这里的package跟java里的类似，main代表的是可执行应用，如果是其它的话表明这是个应用包。import语句是导入包的意思，fmt是系统内建的，Println是fmt中的函数，这个很容易理解。

### 常见的IDE

一直非常喜欢jetbrains的全家桶，但是他们家的go IDE Gogland还没有发布正式版，测试版勉强可以用，下载地址https://www.jetbrains.com/go/?fromMenu。

除此之外，大部分都是文本编辑器+go插件的组合，在github上找到一个列表组合，可以去看看，地址是https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.4.md

### 参考资料

https://golang.org/doc/