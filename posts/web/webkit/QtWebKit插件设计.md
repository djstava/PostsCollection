### 软硬件环境
* Ubuntu Kylin

### 概述
QtWebkit插件的设计可以分为两种情况。一种是MIME类型是application/x-qt-plugin或者application/x-qt-styled-widget，而另一种却无此限制，可以是任意类型。本篇主要介绍第一种，相比于第二种情形也更简单，只需要重新实现方法QObject* QWebPage::createPlugin(const QString &classid,const QUrl &url,const QStringList &paramNames,const QStringList &paramValues)。本文将详细介绍这二种方法的实现。

### 方法一

#### webPlugin.pro
	TEMPLATE = app
	QT += webkit

	HEADERS += myPlugin.h
	SOURCES += main.cpp myPlugin.cpp

#### main.cpp
	#include <QApplication>
	#include <QtGui>

	#include "myPlugin.h"

	int main(int argc, char *argv[])
	{
   		QApplication app(argc, argv);
		MyWebView *webView = new MyWebView;
   		webView->load(QUrl("Test.html"));
   		webView->show();
		app.exec();
	}

#### myPlugin.h
	#ifndef MY_PLUGIN_H
	#define MY_PLUGIN_H
	#include <QWebPage>
	#include <QWebView>

	// Derive from QWebPage, because a WebPage handles
	// plugin creation
	class MyWebPage: public QWebPage
	{
   		Q_OBJECT
   		protected:
      	QObject *createPlugin(
        const QString &classid,
        const QUrl &url,
        const QStringList &paramNames,
        const QStringList & paramValues);

   		public:
      		MyWebPage(QObject *parent = 0);
	};

	// Derive a new class from QWebView for convenience.
	// Otherwise you'd always have to create a QWebView
	// and a MyWebPage and assign the MyWebPage object
	// to the QWebView. This class does that for you
	// automatically.
	class MyWebView: public QWebView
	{
   		Q_OBJECT
   		private:
      	MyWebPage m_page;

   		public:
      		MyWebView(QWidget *parent = 0);
	};

	#endif

#### myPlugin.cpp
	#include <QtCore>
	#include <QtGui>

	#include "myPlugin.h"

	MyWebPage::MyWebPage(QObject *parent):
   		QWebPage(parent)
	{
   	// Enable plugin support
   	settings()->setAttribute(QWebSettings::PluginsEnabled, true);
	}

	QObject *MyWebPage::createPlugin(
   		const QString &classid,
   		const QUrl &,
   		const QStringList &,
   		const QStringList &)
	{
   		QObject *result = 0;
   		if(classid == "pushbutton")
       	//qDebug()<<"pushbutton";
       	result = new QPushButton();
   		else if(classid == "lineedit")
       	//qDebug()<<"lineedit";
       	result = new QLineEdit();
   		if(result)
       		result->setObjectName(classid);
   		return result;
	}

	MyWebView::MyWebView(QWidget *parent):
   		QWebView(parent),
   		m_page(this)
	{
   		setPage(&m_page);
	}

#### Test.html
	<html>
   	<head>
      	<title>QtWebKit Plug-in Test</title>
   	</head>
   	<body>
      	<object type="application/x-qt-plugin" classid="pushbutton" name="mybutton" height=300 width=500>
      	</object>
   	</body>
	</html>

#### 编译运行
	qmake
	make
	./webPlugin


![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/qt/QtWebkit%E6%8F%92%E4%BB%B6%E8%AE%BE%E8%AE%A1(%E4%B8%8A)-01.png)

### 方法二
这种方法相比第一种情形应用更加灵活，不限制MIME类型，扩展性也更好。

#### main.cpp
	#include <QtGui/QApplication>
	#include "pluginexample.h"

	int main(int argc, char *argv[])
	{
    	QApplication app(argc, argv);
    	PluginExample pE;
    	pE.show();
    	return app.exec();
	}

#### pluginexample.h
	#ifndef MAINWINDOW_H
	#define MAINWINDOW_H

	#include <QtGui/QMainWindow>
	#include <QtCore/QDir>
	#include <QtCore/QUrl>

	#include <QtGui/QWidget>
	#include <QtGui/QVBoxLayout>
	#include <QtGui/QFrame>
	#include <QtGui/QDesktopServices>

	#include <QtWebKit/QWebView>
	#include <QtWebKit/QWebPage>
	#include <QtWebKit/QWebFrame>
	#include <QtWebKit/QWebSettings>

	#include <QtCore/QDebug>

	#include "webpluginfactory.h"

	class QWebView;

	class PluginExample : public QMainWindow
	{
    	Q_OBJECT

	public:
    	PluginExample(QWidget *parent = 0);
    	~PluginExample();

	private:
    	QWebView* m_webView;
    	QWebView* createWebView();
	};


	#endif // MAINWINDOW_H

#### pluginexample.cpp
	#include "pluginexample.h"

	/**
 	* Plug-in example main class.
 	*/
	PluginExample::PluginExample(QWidget *parent)
    : QMainWindow(parent)
	{
    	// Create the central widget and set it.
    	QFrame* cW = new QFrame(this);
    	setCentralWidget(cW);

    	// Set the layout to the central widget.
    	QVBoxLayout* layout = new QVBoxLayout(cW);
    	cW->setLayout(layout);
    	layout->setMargin(0);
    	layout->setSpacing(0);

    	// Create the webview which will be used to display the page.
    	m_webView = createWebView();

    	// Add it to the layout.
    	layout->addWidget(m_webView);

    	m_webView->show();
	}

	PluginExample::~PluginExample()
	{
	}

	/**
 	* Creates a new webview
 	*/
	QWebView* PluginExample::createWebView()
	{
    	//使能QWebView的Javascript和Plugins属性
    	QWebSettings* defaultSettings = QWebSettings::globalSettings();
    	// We use JavaScript, so set it to be enabled.
    	defaultSettings->setAttribute(QWebSettings::JavascriptEnabled, true);
    	// Plugins must be set to be enabled to use plug-ins.
    	defaultSettings->setAttribute(QWebSettings::PluginsEnabled,true);

    	QWebView* webView = new QWebView(this);

    	/*
     	* We also pass the web plugin factory to the webview.
     	*/
    	WebPluginFactory* factory = new WebPluginFactory(this);
    	webView->page()->setPluginFactory(factory);

    	webView->load(QUrl("index.html"));
    	return webView;
	}

WebPluginFactory是插件工厂类，主要需要实现的就是QWebPluginFactory中的两个虚函数:
virtual QObject *create(const QString &mimeType, const QUrl &url,
const QStringList &argumentNames,const QStringList & argumentValues )const = 0;virtual QList<Plugin> plugins () const = 0;
create() 方法则根据mimeType等参数来决定创建相应的插件，而plugins()方法是获取所有可用的插件列表。

#### webpluginfactory.h
	#ifndef WEBPLUGINFACTORY_H
	#define WEBPLUGINFACTORY_H

	#include <QWebPluginFactory>

	#include <QLabel>

	#include <QDebug>
	#include <QUrl>
	#include <QWebView>
	#include <QWebFrame>

	class WebPluginFactory : public QWebPluginFactory
	{
    	Q_OBJECT
	public:
    	explicit WebPluginFactory(QObject *parent = 0);
    	QObject * create(const QString & mimeType,
                     const QUrl & url,
                     const QStringList & argumentNames,
                     const QStringList & argumentValues) const;
    	QList<QWebPluginFactory::Plugin> plugins () const;

	signals:

	public slots:

	private:
    	QList<QWebPluginFactory::Plugin> _plugins;
	};

	#endif // WEBPLUGINFACTORY_H

#### webpluginfactory.cpp
	#include <QtDebug>
	#include "webpluginfactory.h"

	WebPluginFactory::WebPluginFactory(QObject *parent) :
    	QWebPluginFactory(parent)
	{
    	// initialise the _plugins QList
    	// so that we can use it later in the plugins() method
    	QWebPluginFactory::Plugin plugin;
    	plugin.name = "Qt webkit Plugin";

    	QWebPluginFactory::MimeType mimetype;
    	mimetype.description = "Embedded Qt Widget";
    	mimetype.fileExtensions = QStringList();
    	mimetype.name = "application/x-qt-exampleplugin";
    	plugin.mimeTypes.append(mimetype);

    	_plugins.append(plugin);
	}

	QObject * WebPluginFactory::create(const QString & mimeType,
                 const QUrl & url,
                 const QStringList & argumentNames,
                 const QStringList & argumentValues) const
	{
    	Q_UNUSED(url);

    	if (("application/x-qt-exampleplugin" != mimeType)) {
        	return new QObject();
    }
    /*
    *** 对plugin的相应操作 ****
    */
    Q_UNUSED(argumentNames);
    Q_UNUSED(argumentValues);

    qDebug()<<"This is an MIME=application/x-qt-exampleplugin.";
    qDebug()<<"All the parameters is:"<<argumentNames<<" and "<<argumentValues;

    return NULL;
	}

	/**
 	* Returns supported plug-ins.
 	* Currently, this function is only called when JavaScript
 	* programs access the global plug-ins or MIME type objects.
 	*/
	QList<QWebPluginFactory::Plugin> WebPluginFactory::plugins () const
	{
    	return _plugins;
	}

#### pluginexample.pro
	QT += core \
      	  gui \
          webkit

	TARGET = pluginexample
	TEMPLATE = app

	SOURCES += main.cpp\
        pluginexample.cpp \
    	webpluginfactory.cpp

	HEADERS += pluginexample.h \
    	webpluginfactory.h

#### 测试页面index.html
	<html>
    <head>
        <title>Plugin example page</title>
    </head>

    <body id="body">
            <h2>QtWebKit plugin</h2>
            <object id="describePlugin"
                    type="application/x-qt-exampleplugin"
                    width="100%" height="80%">
            </object>

    </body>
	</html>

#### 编译运行程序
	qmake
	make
	./pluginexample

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/qt/QtWebkit%E6%8F%92%E4%BB%B6%E8%AE%BE%E8%AE%A1(%E4%B8%8B)-01.png)
