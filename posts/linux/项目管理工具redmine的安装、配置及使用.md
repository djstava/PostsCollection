### 软硬件环境
* ubuntu 12.04

### 前言

Redmine 是一个开源的、基于Web的项目管理和缺陷跟踪工具。它用日历和甘特图辅助项目及进度可视化显示。同时它又支持多项目管理。Redmine是一个自由开放 源码软件解决方案，它提供集成的项目管理功能，问题跟踪，并为多个版本控制选项的支持。

### 安装

	sudo apt-get install tasksel
	sudo tasksel install lamp-server
	sudo apt-get install mysql-server libapache2 libapache2-mod-passenger redmine redmine-mysql

### 配置

	sudo ln -s /usr/share/redmine/public /var/www/redmine
默认情况下，passenger是以nobody来运行的，将它改成www-data，这是apache2默认用户    
	sudo vi /etc/apache2/mods-available/passenger.conf,增加如下语句     

	PassengerDefaultUser www-data

#### 关联apache2
sudo vi /etc/apache2/sites-available/default，增加如下语句

	<Directory /var/www/redmine>
		RailsBaseURI /redmine
		PassengerResolveSymlinksInDocumentRoot on
	</Directory>

#### Enable passenger

    sudo a2enmod passenger

#### Restart apache2
    sudo /etc/init.d/apache2 restart

### 测试
192.168.95.134是安装Redmine的IP地址

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/redmine/Redmine-01.png)

### 中文乱码问题

在安装好Redmine后，中文创建工程时会出现中文乱码问题，这是redmine数据库默认字符编码的问题。首先删除数据库中的数据表。打开phpmyadmin(如http://192.168.95.134/phpmyadmin),找到数据库redmine_default，删除。然后再重新设置下redmine，默认redmine language选择zh    

	sudo dpkg-reconfigure redmine

再次打开redmine（如http://192.168.95.134/redmine/），以管理员身份(默认用户名和密码都是admin)，用中文新建工程，再次查看乱码没有了。

### 增加自动发送邮件功能

#### 安装配置sendmail

	sudo apt-get install sendmail
	sudo sendmailconfig（一路选择Y）

#### 修改邮件配置文件

	cd /usr/share/redmine/config
	cp email.yml.example email.yml

将email.yml修改成如下:

	 production:
      delivery_method: :sendmail
      sendmail_settings:
      address: smtp.example.net
      port: 25
      domain: example.net
      authentication: :login
      user_name: "redmine@example.net"
      password: "redmine"

使能redmine邮件发送功能

	sudo vi /usr/share/redmine/config/environment.rb

将config.action_mailer.perform_deliveries = false 改为 config.action_mailer.perform_deliveries = true

重启apache2服务

	sudo /etc/init.d/apache2 restart


### Redmine关联svn

搭建svn server
#### 软件安装

	sudo apt-get install subversion subversion-tools

#### 创建仓库

	svnadmin create /home/djstava/SVN

#### 修改配置文件/home/djstava/SVN/conf/svnserve.conf
	打开以下几项(要顶格)
	anon-access = read
	auth-access = write
	password-db = passwd

#### 修改配置文件/home/djstava/SVN/conf/passwd,增加用户名及密码
	[users]
	djstava = djstava

#### 导入源代码
	svn import /home/djstava/flash svn://192.168.18.100/flash

#### 启动服务
	svnserver -d -r /home/djstava/SVN

#### Redmine关联svn
在具体工程内选择版本库，这里边支持的工具很多，选择Subversion，并填上svn地址、用户名和密码就OK了。

### 备份与恢复

#### 备份
* 数据库

    `mysqldump -uroot -pdjstava redmine_default > redmine_bak.sql`（redmine_default是默认的数据库，-u用户名,-p密码）

* 附件

    所有上传到redmine上的文件都会保存在/var/lib/redmine/default/files目录下

* 配置文件

	备份相关的配置文件，路径是/usr/share/redmine/config，如之前提到的邮件发送服务配置文件

#### 恢复
* 数据库

	mysqldump -uroot -pdjstava redmine_default < redmine_bak.sql
* 附件

* 配置文件

	上述两项只需要将相应文件拷贝到相应目录即可

### 邮件超链接无法跳转到redmine的问题

#### 查看机器hostname

	$hostname(如djstava)

#### 修改Redmine配置

以admin身份登录redmine，进入`Administration-->Settings-->General`项，修改的值Host name and path的值，其实这里也可以不设置成机器的hostname，直接填写IP地址也可以。
#### 重启redmine

	sudo /etc/init.d/apache2 restart


### 安装Roadmap插件
这个插件是一日本人开发，主页在<https://github.com/daipresents/redmine_roadmaps>.

	cd /usr/share/redmine
	sudo git clone git://github.com/daipresents/remind_roadmaps.git vendor/plugins/redmine_roadmaps

#### 重启redmine，以admin身份登录管理界面
1、Administration-->Roles and permissions-->Permissions report

2、Project->Settings->Modules

3、Project->Settings->Version

4、解决使能插件后出现的translation missing: zh roadmaps_name问题

	cd /usr/share/redmine/vendor/plugins/redmine_roadmaps/config/locales
	sudo cp en.yml zh.yml

	zh:
    	roadmaps_name: "里程碑"
    	edit: "编辑"
    	late: "%{days} days late"
    	days_to_go: "%{days} days to go"
    	version_effective_date: "version's effective date"
    	finished: "finished"
    	unfinished: "unfinished"
    	estimated_hours: "Estimated Hours"
    	passed_hours: "Hours"
    	hours: "hours"
    	actual_date: "actual date"
    	assigned_users: "assigned user"
    	descripton: "description"
    	ticket_status: "ticket status"
    	man_hours: "man hours"

    	no_start_date: "start date is not setting"
    	no_due_date: "due date is not setting"
    	no_date: "start date and due date are not setting"
    	no_version_effective_date: "version effective date is not setting"

我只修改了一部分，根据需要自行修改即可，重启redmine，那串不和谐的字符就不见了。

### 参考文献
1、<http://www.redmine.org/plugins/roadmaps>    
2、<https://github.com/daipresents/redmine_roadmaps>
