### 软硬件环境
* ubuntu 12.10
* Qt 4.7

### 前言
所谓的Qt javascript扩展指的是将QObject对象抛出给webkit和javascript，这样前端就可以通过js去调用QObject对象的方法，这里的方法是指QObject的public slots，另外QObject的属性在javascript中也是可用的，QObject无法由QtWebkit显式地删除。

来看示例

### main.cpp
	#include <qapplication.h>
	#include <qwebview.h>

	#include "testobject.h"

	int main( int argc, char **argv )
	{
    	QApplication app( argc, argv );

    	QWebView *view = new QWebView();

    	MyClass *myclass = new MyClass( view );
    	myclass->setWebView( view );

    	view->setUrl( QUrl( "test.html" ) );
    	view->show();

    	return app.exec();
	}

### test.pro
	TEMPLATE = app
	TARGET =
	DEPENDPATH += .
	INCLUDEPATH += .

	QT += webkit

	# Input
	HEADERS += testobject.h
	SOURCES += main.cpp testobject.cpp

### testobject.h
	#ifndef TESTOBJECT_H
	#define TESTOBJECT_H

	#include <qobject.h>

	class QWebView;
	class QWebFrame;

	class MyClass : public QObject
	{
    	Q_OBJECT
	public:
    	MyClass( QObject *parent );

    	void setWebView( QWebView *view );

	public slots:
    	void print( const QString &param );

	private slots:
    	void attachObject();

	private:
    	QWebFrame *frame;
	};

	#endif // TESTOBJECT_H

### testobject.cpp
	#include <qdebug.h>
	#include <qwebview.h>
	#include <qwebframe.h>

	#include "testobject.h"

	MyClass::MyClass( QObject *parent )
    	: QObject( parent )
	{

	}

	void MyClass::setWebView( QWebView *view )
	{
    	QWebPage *page = view->page();
    	frame = page->mainFrame();

    	attachObject();
    	connect( frame, 	SIGNAL(javaScriptWindowObjectCleared()), this, SLOT(attachObject()) ); //当网页被载入或者刷新时，将暴露给webkit的QObject和JavaScript进行连接
	}

	void MyApi::attachObject()
	{
    	frame->addToJavaScriptWindowObject( QString("MyApi"), this );//前提是使能javascript，即将JavascriptEnabled属性值设置为true;
	}

	void MyClass::print( const QString &param )
	{

    	//这是最终被javascript脚本调用的函数
    	qDebug() << "Print paramter from js script:" << param;
	}

### test.html
	<html>
	<body>
	Test Page for Qt Javascript Extension!
	<!--
	这里调用MyClass的槽函数print
 	-->
	<script>
  		MyClass.print( 'Test Page for Qt Javascript Extension!' );  
	</script>
	</body>
	</html>

### 编译运行
	qmake
	make
	./test

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/qt/Qt%20Javascrip%E6%89%A9%E5%B1%95-01.png)
