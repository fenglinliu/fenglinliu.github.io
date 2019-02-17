---
tags: [大数据]
---
## <center>SparkProject记录</center>

#### 1.配置服务器环境（每台机器都需要配置）

##### 1.1 设置静态IP和修改hosts文件

##### 1.2 关闭IPV6和selinux、防火墙

##### 1.3 安装JDK

在一台机器上装好之后直接复制过去

```
在/user/local目录下执行：
scp -r ./jdk1.8.0_121 root@sparkproject-02:/usr/local/
scp /etc/profile root@sparkproject-02:/etc/

scp -r ./jdk1.8.0_121 root@sparkproject-03:/usr/local/
scp /etc/profile root@sparkproject-03:/etc/
```

##### 1.4 ssh免密码登录

三台机器上配置对本机的ssh免密码登录，接着配置三台机器互相之间的ssh免密码登录

#### 2.安装hadoop

hadoop-2.5.0-cdh5.3.6.tar.gz上传到01机，先在01上做配置

##### 2.1 修改hadoop配置

hadoop的配置文件都在`/usr/local/hadoop/etc/hadoop`目录下

* 修改`/etc/profile`文件，添加环境变量配置

```
export HADOOP_HOME=/usr/local/hadoop
exoprt PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

* 修改hadoop-env.sh

```xml
# The java implementation to use.
export JAVA_HOME=/usr/local/jdk1.8.0_121
```

* 修改core-site.xml

```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://sparkproject-01:9000</value>
  </property>
</configuration>
```

* 修改hdfs-site.xml

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.name.dir</name>
    <value>/usr/local/data/namenode</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>/usr/local/data/datanode</value>
  </property>
  <property>
    <name>dfs.tmp.dir</name>
    <value>/usr/local/data/tmp</value>
  </property>
</configuration>
```

* 修改mapred-site.xml

```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

* 修改yarn-site.xml

```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>sparkproject-01</value>
  </property>
</configuration>
```

* 修改slaves文件

```
 // 先顺除掉localhost， 再添加
sparkproject-02
sparkproject-03
```

##### 2.2 同步配置到其余主机（从节点）

记得在sparkproject-02和sparkproject-03的/usr/local目录下创建data目录

```
// 用scp命令把hadoop安装包和/etc/profile配置文件
scp -r /usr/local/hadoop root@sparkproject-02:/usr/local
scp /etc/profile root@sparkproject-02:/etc/

scp -r /usr/local/hadoop root@sparkproject-03:/usr/local
scp /etc/profile root@sparkproject-03:/etc/

// /etc/profile配置文件进行source，以让它生效。
source /etc/profile
```

#####2.3 启动HDFS集群

* 格式化namenode：在sparkproject-01（主节点）上执行以下命令

`hdfs namenode -format`

* 启动HDFS集群

```
[root@sparkproject-01 sbin]# start-dfs.sh
18/05/16 16:36:45 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [sparkproject-01]
sparkproject-01: starting namenode, logging to /usr/local/hadoop/logs/hadoop-root-namenode-sparkproject-01.bookcycle.cn.out 【sparkproject-01上启动namenode】
sparkproject-02: starting datanode, logging to /usr/local/hadoop/logs/hadoop-root-datanode-sparkproject-02.bookcycle.cn.out 【sparkproject-02上启动datanode】
sparkproject-03: starting datanode, logging to /usr/local/hadoop/logs/hadoop-root-datanode-sparkproject-03.bookcycle.cn.out 【sparkproject-03上启动datanode】
Starting secondary namenodes [0.0.0.0]
The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
ECDSA key fingerprint is SHA256:7CINJAzRNELAEMIz9eWh1s/PNrhLQi9TO6sMqVvUp78.
ECDSA key fingerprint is MD5:cd:fe:54:8d:f2:e3:a5:ba:a9:8a:9d:f0:82:5f:36:53.
Are you sure you want to continue connecting (yes/no)? yes
0.0.0.0: Warning: Permanently added '0.0.0.0' (ECDSA) to the list of known hosts.
0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop/logs/hadoop-root-secondarynamenode-sparkproject-01.bookcycle.cn.out 【sparkproject-01上启动secondarynamenode】
18/05/16 16:37:12 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

主节点上启动了NameNode、SecondaryNameNode，从节点上启动了DataNode

查看sparkproject-01上的java进程，NameNode、SecondaryNameNode启动成功

```
[root@sparkproject-01 sbin]# jps
1925 NameNode
2200 Jps
2091 SecondaryNameNode
```

查看sparkproject-02上的java进程，DataNode启动成功

```
[root@sparkproject-02 local]# jps
1776 Jps
1708 DataNode
```

查看sparkproject-03上的java进程，DataNode启动成功

```
[root@sparkproject-03 local]# jps
1699 DataNode
1768 Jps
```

* 访问HDFS管理界面

 http://sparkproject-01:50070  

##### 2.4 启动yarn集群

* start-yarn.sh

```
[root@sparkproject-01 sbin]# start-yarn.sh
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop/logs/yarn-root-resourcemanager-sparkproject-01.bookcycle.cn.out 【sparkproject-01上启动resourcemanager】
sparkproject-03: starting nodemanager, logging to /usr/local/hadoop/logs/yarn-root-nodemanager-sparkproject-03.bookcycle.cn.out 【sparkproject-03上启动nodemanager】
sparkproject-02: starting nodemanager, logging to /usr/local/hadoop/logs/yarn-root-nodemanager-sparkproject-02.bookcycle.cn.out 【sparkproject-02上启动nodemanager】
```

主节点上启动了resourcemanager，从节点上启动了nodemanager

```
[root@sparkproject-01 sbin]# jps
2336 ResourceManager
2610 Jps
1925 NameNode
2091 SecondaryNameNode

--------------------------

[root@sparkproject-02 local]# jps
1831 NodeManager
1708 DataNode
1950 Jps

--------------------------

[root@sparkproject-03 local]# jps
1699 DataNode
1945 Jps
1823 NodeManager
```

* 访问YARN管理界面

  http://sparkproject-01:8088 

  ​

#### 3. 安装Hive

hive-0.13.1-cdh5.3.6.tar.gz上传到01机，只在01上做配置

##### 3.1 安装mysql（主节点）

* 主节点安装MySQL，并设置开机自启动（按照Linux下MySQL安装配置）

* 执行`yum install -y mysql-connector-java`，获取mysql-connector-java.jar （如果已经有了mysql-connector-java.jar，可以不用这条命令，避免这条命令升级JDK之类的）将`/usr/share/java/mysql-connector-java.jar`拷贝到`/usr/local/hive/lib`

* 在mysql上创建hive元数据库，创建hive账号，并进行授权

  ```
  create database if not exists hive_metadata; 【创建hive的元数据库】
  grant all privileges on hive_metadata.* to 'hive'@'%' identified by 'hive'; 【创建hive账号，密码也是hive，对来自任意地址登陆的hive用户对hive_metadata有所有权限】
  grant all privileges on hive_metadata.* to 'hive'@'localhost' identified by 'hive'; 【对来自localhost地址登陆的hive用户对hive_metadata有所有权限】
  grant all privileges on hive_metadata.* to 'hive'@'sparkproject-01.bookcycle.cn' identified by 'hive';【对来自sparkproject-01.bookcycle.cn地址登陆的hive用户对hive_metadata有所有权限】
  【经过后面两个授权后，任意地址将不能操纵hive，sparkproject-01.bookcycle.cn，一定要带上.bookcycle.cn域名】
  flush privileges;
  use hive_metadata; 【测试数据库是否创建成功】
  ```

##### 3.2 修改hive配置

hive的配置文件都在`/usr/local/hive/conf`目录下

- 修改`/etc/profile`文件，添加环境变量配置

```
export HIVE_HOME=/usr/local/hive
exoprt PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin
```

执行`source /etc/profile`，刷新配置

* 修改hive-site.xml

```xml
// 首先，将hive-default.xml.template 另存为 hive-site.xml
cp hive-default.xml.template hive-site.xml
vi hive-site.xml
// 修改如下配置

<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://sparkproject-01:3306/hive_metadata?createDatabaseIfNotExist=true</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>com.mysql.jdbc.Driver</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>hive</value>
</property>

<property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value>hive</value>
</property>
```

* 修改hive-env.sh

```
// 首先，将hive-env.sh.template 另存为 hive-env.sh，并增加如下内容
export JAVA_HOME=/usr/local/jdk1.8.0_121
export HADOOP_HOME=/usr/local/hadoop
export HIVE_HOME=/usr/local/hive
```

#####3.3 测试hive

```
[root@sparkproject-01 ~]# hive
18/05/16 20:27:52 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead

Logging initialized using configuration in jar:file:/usr/local/hive/lib/hive-common-0.13.1-cdh5.3.6.jar!/hive-log4j.properties
hive> eixt;
```

#### 4.安装Zookeeper

上传zookeeper-3.4.5-cdh5.3.6.tar.gz到01机，先再01机上做配置

##### 4.1 修改Zookeeper配置

Zookeeper的配置文件在，`/usr/local/zookeeper/conf`

* 修改`/etc/profile`文件，添加环境变量配置

```xml
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin::$HIVE_HOME/bin:$ZOOKEEPER_HOME/bin
```

执行`source /etc/profile`，刷新配置

* 配置zoo.cfg

```
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg
修改：dataDir=/usr/local/zk/data
在最后加上：（表示三个节点，一般要求是奇数个节点）
server.0=sparkproject-01:2888:3888
server.1=sparkproject-02:2888:3888
server.2=sparkproject-03:2888:3888
```

* 建立/usr/local/zk/data目录

在`/usr/local/zk/data`目录下执行`vi myid`，文件的值给0

```
[root@sparkproject-01 data]# cat myid 
0
```

##### 4.2 同步到其他节点

```
scp -r /usr/local/zookeeper root@sparkproject-02:/usr/local
scp -r /usr/local/zk root@sparkproject-02:/usr/local
scp /etc/profile root@sparkproject-02:/etc/
source /etc/profile

scp -r /usr/local/zookeeper root@sparkproject-03:/usr/local
scp -r /usr/local/zk root@sparkproject-03:/usr/local
scp /etc/profile root@sparkproject-03:/etc/
source /etc/profile

// 修改第2、3台机器的myid分别为1,2
[root@sparkproject-02 data]# cat myid 
1

[root@sparkproject-03 data]# cat myid 
2
```

分别在三台机器上执行：zkServer.sh start

```
[root@sparkproject-02 data]# zkServer.sh start
JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

```

检查ZooKeeper状态

```
[root@sparkproject-01 ~]# zkServer.sh status
JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: follower

​~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

[root@sparkproject-02 ~]# zkServer.sh status
JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: follower

​~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

[root@sparkproject-03 ~]# zkServer.sh status
JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: leader
【可以看到有一个leader和两个follower】
```

再用jps：检查三个节点是否都有QuromPeerMain进程。



#### 5.Kafka搭建

scala-2.11.4.tgz、kafka_2.9.2-0.8.1.tgz上传到01机，先再01机上做配置

##### 5.1 Scala安装

* 修改/etc/profile文件

```
export SCALA_HOME=/usr/local/scala
export PATH=$PATH:$SCALA_HOME/bin
```

`source /etc/profile`刷新配置

* 查看scala是否安装成功

```
[root@sparkproject-01 scala]# scala -version
Scala code runner version 2.11.4 -- Copyright 2002-2013, LAMP/EPFL
```

##### 5.2 同步Scala到其余节点

```
scp -r /usr/local/scala root@sparkproject-02:/usr/local
scp /etc/profile root@sparkproject-02:/etc/
source /etc/profile

scp -r /usr/local/scala root@sparkproject-03:/usr/local
scp /etc/profile root@sparkproject-03:/etc/
source /etc/profile
```

##### 5.3 安装Kafka

kafka的配置文件在/usr/local/kafka/config目录下

* 配置kafka

```
// 修改部分内容
broker.id=0
zookeeper.connect=192.168.38.141:2181,192.168.38.142:2181,192.168.38.143:2181 【一定要用IP地址】
```

* 安装slf4j

slf4j-1.7.6.zip上传到01机/usr/local目录下，执行`unzip slf4j-1.7.6.zip`，然后把slf4j中的slf4j-nop-1.7.6.jar复制到kafka的libs目录下面

```
cp /usr/local/slf4j-1.7.6/slf4j-nop-1.7.6.jar /usr/local/kafka/libs/
```

##### 5.4 同步Kafka到其余节点

```
scp -r /usr/local/kafka root@sparkproject-02:/usr/local
scp -r /usr/local/kafka root@sparkproject-03:/usr/local

// 唯一的区别是server.properties中的broker.id，要设置为1和2
```

##### 5.5 启动kafka集群

```
// 在三台机器的kafka目录下，执行如下命令
nohup bin/kafka-server-start.sh config/server.properties &
```

```
因为kafkak可能会不支持我们当前机器上的JDK版本：
1、解决kafka Unrecognized VM option 'UseCompressedOops'问题
vi /usr/local/kafka/bin/kafka-run-class.sh 
if [ -z "$KAFKA_JVM_PERFORMANCE_OPTS" ]; then
  KAFKA_JVM_PERFORMANCE_OPTS="-server  -XX:+UseCompressedOops -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -XX:+CMSScavengeBeforeRemark -XX:+DisableExplicitGC -Djava.awt.headless=true"
fi
去掉-XX:+UseCompressedOops即可

另外两台机器上也要这样做！！！！
scp /usr/local/kafka/bin/kafka-run-class.sh root@sparkproject-02:/usr/local/kafka/bin/
scp /usr/local/kafka/bin/kafka-run-class.sh root@sparkproject-03:/usr/local/kafka/bin/

然后启动kafka
[root@sparkproject-01 kafka]# nohup bin/kafka-server-start.sh config/server.properties &
[1] 1980
[root@sparkproject-01 kafka]# nohup: 忽略输入并把输出追加到"nohup.out"
```

检查kafka是否启动成功（也可以执行`cat nohup.out`）

```
[root@sparkproject-01 kafka]# jps
2024 Jps
1980 Kafka
1725 QuorumPeerMain

[root@sparkproject-02 kafka]# jps
2868 Kafka
2908 Jps
2221 QuorumPeerMain

[root@sparkproject-03 kafka]# jps
2915 Jps
2216 QuorumPeerMain
2876 Kafka
```

测试kafka集群（！！！！！存在问题）

```
[root@sparkproject-01 kafka]# bin/kafka-topics.sh --zookeeper 192.168.38.141:2181,192.168.38.142:2181,192.168.38.143:2181 --topic Test2Topic --replication-factor 1 --partitions 1 --create
Created topic "TestTopic". 【创建一个队列】

[root@sparkproject-01 kafka]# bin/kafka-console-producer.sh --broker-list 192.168.38.141:2181,192.168.38.142:2181,192.168.38.143:2181 --topic Test2Topic 【创建生产者】

[root@sparkproject-02 kafka]# ssh sparkproject-01
Last login: Thu May 17 14:15:24 2018 from 192.168.38.1
[root@sparkproject-01 ~]# cd ..
[root@sparkproject-01 /]# cd /usr/local/kafka
[root@sparkproject-01 kafka]# bin/kafka-console-consumer.sh --zookeeper 192.168.38.141:2181,192.168.38.142:2181,192.168.38.143:2181 --topic Test2Topic --from-beginning 【创建消费者】

```

#### 6.Flume搭建

现在01机上做配置，然后在同步到其他机器

#####6.1 配置flume 

/usr/local/flume/conf/flume-conf.properties

* 修改/etc/profile

  ```
  export FLUME_HOME=/usr/local/flume
  export FLUME_CONF_DIR=$FLUME_HOME/conf
  export PATH=$FLUME_HOME/bin

  ```

* 修改flume配置文件

```
cp flume-conf.properties.template flume-conf.properties


agent1.sources=source1
agent1.sinks=sink1
agent1.channels=channel1
修改为
#agent1表示代理名称
agent1.sources=source1
agent1.sinks=sink1
agent1.channels=channel1

agent.sources.seqGenSrc.channels = memoryChannel
修改为
#配置source1
agent1.sources.source1.type=spooldir
agent1.sources.source1.spoolDir=/usr/local/logs
agent1.sources.source1.channels=channel1
agent1.sources.source1.fileHeader = false
agent1.sources.source1.interceptors = i1
agent1.sources.source1.interceptors.i1.type = timestamp

agent.channels.memoryChannel.capacity = 100
agent.channels.memoryChannel.type = memory
修改为
#配置channel1
agent1.channels.channel1.type=file
agent1.channels.channel1.checkpointDir=/usr/local/logs_tmp_cp
agent1.channels.channel1.dataDirs=/usr/local/logs_tmp

agent.sinks.loggerSink.channel = memoryChannel
修改为
#配置sink1
agent1.sinks.sink1.type=hdfs
agent1.sinks.sink1.hdfs.path=hdfs://sparkproject-01:9000/logs
agent1.sinks.sink1.hdfs.fileType=DataStream
agent1.sinks.sink1.hdfs.writeFormat=TEXT
agent1.sinks.sink1.hdfs.rollInterval=1
agent1.sinks.sink1.channel=channel1
agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d

```

* 创建文件夹

```
本地文件夹：mkdir /usr/local/logs
HDFS文件夹：hdfs dfs -mkdir /logs (要确保hdfs已经启动)
```

##### 6.2 启动flume-agent

```
[root@sparkproject-01 conf]# flume-ng agent -n agent1 -c conf -f /usr/local/flume/conf/flume-conf.properties -Dflume.root.logger=DEBUG,console

然后新建一个窗口进入01机，进行测试：
新建一份文件，移动到/usr/local/logs目录下，flume就会自动上传到HDFS的/logs目录中
[root@sparkproject-01 ~]# cd ..
[root@sparkproject-01 /]# cd /usr/local
[root@sparkproject-01 local]# vi ids
[root@sparkproject-01 local]# mv ids logs
[root@sparkproject-01 local]# hdfs dfs -lsr /logs
lsr: DEPRECATED: Please use 'ls -R' instead.
18/05/17 17:21:04 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
-rw-r--r--   2 root supergroup        985 2018-05-17 17:20 /logs/2018-05-17.1526548803616
```

#### 7.Spark搭建

先再01机上配置，然后再同步到其他机器

##### 7.1 修改/etc/profile

```
export SPARK_HOME=/usr/local/spark
export PATH=$SPARK_HOME/bin
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

##### 7.2 修改spark-env.sh

再目录 /usr/local/spark/conf下

```
cp spark-env.sh.template spark-env.sh
vi spark-env.sh
// 再末尾增加以下内容
export JAVA_HOME=/usr/local/jdk1.8.0_121
export SCALA_HOME=/usr/local/scala
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
```

