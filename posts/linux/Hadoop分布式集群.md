###　软硬件环境

* ubuntu server 16.04 64bit
* jdk 1.8.0_151
* hadoop 2.8.2

### Hadoop简介

Hadoop是Apache基金会开发的一款分布式集群系统，包括HDFS(Hadoop Distributed File System)和MapReduce(提供并行计算)2个核心部件。HDFS为海量的数据提供了存储，而且是在价格低廉的硬件上。Hadoop有如下优点:

* 高可靠性

文件以块的形式存储，默认情况下系统中的每一块都有2个备份，大大提升了可靠性

* 高可扩展性

可以很方便的进行系统扩容

* 低成本

不必使用昂贵的专业存储设备，大大降低使用成本

### 准备工作

手头上刚好有几台服务器，分别将它们安装上ubuntu server 16.04 64位操作系统并使用相同的用户名及密码，方便后续操作。将主机名及IP地址进行一个分配如下

```
10.0.0.212 master212
10.0.0.213 slave213
10.0.0.177 slave177
```

将上面3条语句分别添加到3台服务器中的/etc/hosts文件中，并根据配置的IP地址修改相应的/etc/hostname文件，操作完毕后，用ping主机名的方式测试配置是否成功。

hadoop是用java语言开发的，因此jdk是必须要安装的。

下载jdk-8u151-linux-x64.tar.gz，地址<http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>

```
cd /home/longjing
tar xvf jdk-8u151-linux-x64.tar.gz
mv jdk-8u151-linux-x64 jdk
```

接下来下载hadoop-2.8.2.tar.gz，地址<http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.8.2/hadoop-2.8.2.tar.gz>

```
cd /home/longjing
tar xvf hadoop-2.8.2.tar.gz
mv hadoop-2.8.2 hadoop
```

至此，准备工具就绪，开始设置环境变量

编辑系统配置文件/etc/profile，添加语句

```
export JAVA_HOME=/home/longjing/jdk
export JRE_HOME=/home/longjing/jdk/jre
export HADOOP_HOME=/home/longjing/hadoop
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$PATH
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
```

然后执行

```
source /etc/profile
```

使配置立即生效。上述操作中hadoop工具包的上传可以先只在master212上进行，因为后续还要修改hadoop的配置文件，修改成功后一并拷贝到slave213和slave177中。除此之外，其他操作在3台机器上都需要进行操作。

### SSH设置

从这一步开始，如无特别提示，操作均都只在master212上进行

```
ssh-keygen -t rsa
cd .ssh
cat id_rsa.pub >> authorized_keys
scp authorized_keys longjing@slave213:/home/longjing/.ssh
scp authorized_keys longjing@slave177:/home/longjing/.ssh
```

###Hadoop配置

创建需要的文件目录

```
mkdir -p hadoop/tmp
mkdir -p hadoop/hdfs/name
mkdir -p hadoop/hdfs/data
```

编辑hadoop/etc/core-site.xml

```
<configuration>
<property>
 <name>fs.defaultFS</name>
    <value>hdfs://master212:9000</value>
</property>
<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/home/longjing/hadoop/tmp</value>
</property>
</configuration>
```

编辑hadoop/etc/hadoop/hadoop-env.sh，修改JAVA_HOME

```
export JAVA_HOME=/home/longjing/jdk
```

编辑hadoop/etc/hadoop/mapred-site.xml

```
<configuration>
<property>
 <name>fs.defaultFS</name>
    <value>hdfs://master212:9000</value>
</property>
<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/home/longjing/hadoop/tmp</value>
</property>
</configuration>
```

编辑hadoop/etc/hadoop/yarn-site.xml

```
<configuration>
<!-- Site specific YARN configuration properties -->
<property>
 <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
    <name>yarn.resourcemanager.address</name>
    <value>master212:8032</value>
</property>
<property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>master212:8030</value>
</property>
<property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>master212:8031</value>
</property>
<property>
    <name>yarn.resourcemanager.admin.address</name>
    <value>master212:8033</value>
</property>
<property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>master212:8088</value>
</property>
</configuration>
```

编辑hadoop/etc/hadoop/hdfs-site.xml

```
<configuration>
<property>
 <name>dfs.namenode.name.dir</name>
    <value>file:/home/longjing/hadoop/hdfs/name</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/home/longjing/hadoop/hdfs/data</value>
</property>
<property>
    <name>dfs.replication</name>
    <value>2</value>
</property>
<property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>master212:9001</value>
</property>
</configuration>
```

格式化

```
hdfs namenode -format
```

这里简单提一下，hadoop中有namenode和datanode两个概念。namenode就是这里的master212节点，它负责记录存储文件的元数据，而真正的block块是存放在datanode里的。

### hadoop配置同步

上述针对hadoop配置文件的修改完成后，就可以直接将hadoop文件夹发送给slave213和slave177了

```
scp -r hadoop slave213:/home/longjing
scp -r hadoop slave177:/home/longjing
```

### 启动Hadoop

```
cd hadoop/sbin
./start-all.sh
```

###测试环境

通过命令行查看hadoop是否运行正常

![hadoop_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_06.png)



![hadoop_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_05.png)

hadoop内置了一个web服务器，可以通过web的形式查看系统状态，信息量也比较大

![hadoop_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_01.png)

![hadoop_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_02.png)

![hadoop_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_03.png)

![hadoop_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/hadoop/hadoop_04.png)

### 基本的hadoop shell

hadoop也实现了一套类似于linux shell的工具集。可以通过命令来对hdfs进行操作，具体可以通过帮助命令来查看

```
hadoop fs help
```

### 参考资料

<http://blog.csdn.net/hongbochen1223/article/details/72872472>

<http://blog.csdn.net/beliefer/article/details/51073294>

<http://blog.csdn.net/wang_walfred/article/details/41281337>

<https://wiki.apache.org/hadoop/BuildFuseDfs023>