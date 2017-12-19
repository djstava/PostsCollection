### 软硬件环境

- Windows 10
- PyQt5 
- Python3.4

### 前言

在PyQt5编程中，经常需要在某个signal发送时附带相应的数据，实际上就是传参，然后在slot方法中接收传递过来的数据，这样就可以非常灵活地实现一些业务逻辑。好了，本文就来实现这个功能。

### 实践

首先是生成一个signal

```
transcoding_thread_status = pyqtSignal(object)
```

然后将signal和slot方法绑定

```
self.transcoding_thread_status.connect(self.slotShowTransThreadStatus)
```

slot方法接收数据

```
def slotShowTransThreadStatus(something):
	print(something)
```

最后来看看，signal emit时是怎么实现的

```
self.transcoding_thread_status.emit("Hello")
```

至此数据就传递成功了，就是这么简单。

以上是传递一个参数，那如果我需要传递2个呢，是不是也可以用上面的方法呢？思路是一样的，细节做一点小修改就好了。

```
transcoding_thread_status = pyqtSignal(str,str)
```

如果传递的数据类型是整形的话，就换成int，对应的slot方法也要跟着修改

```
def slotShowTransThreadStatus(something,anything):
	print(something)
	print(anything)
```

发送信号时，就跟上2个参数

```
self.transcoding_thread_status.emit("Hello","world")
```

类似的，如果需要传递更多参数，请依葫芦画瓢。

### 源码下载

https://github.com/djstava/troutling，这个项目是一个带GUI的ffmpeg wrapper。因为最近正在做一个电视直播的项目，其中涉及到音视频的实时再编码及HLS相关的内容，为了更加方便的进行节目源(目前是UDP的流)转码，就做了个这个小工具。使用的是Windows+PyQt5+Python3。