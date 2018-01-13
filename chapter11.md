# chapter11

由于我Spark采用的是Cloudera公司的CDH，并且安装的时候是在线自动安装和部署的集群。最近在学习SparkSQL，看到SparkSQL on HIVE。下面主要是介绍一下如何通过SparkSQL在读取HIVE的数据。

（说明：如果不是采用CDH在线自动安装和部署的话，可能需要对源码进行编译，使它能够兼容HIVE。

编译的方式也很简单，只需要在Spark\_SRC\_home（源码的home目录下）执行如下命令：

./make-distribution.sh --tgz -Phadoop-2.2 -Pyarn -DskipTests -Dhadoop.version=2.6.0-cdh5.4.4 -Phive

编译好了之后，会在lib目录下多几个jar包。）

下面我主要介绍一下我使用的情况：

1、为了让Spark能够连接到Hive的原有数据仓库，我们需要将Hive中的hive-site.xml文件拷贝到Spark的conf目录下，这样就可以通过这个配置文件找到Hive的元数据以及数据存放。

在这里由于我的Spark是自动安装和部署的，因此需要知道CDH将hive-site.xml放在哪里。经过摸索。该文件默认所在的路径是：/etc/hive/conf 下。

同理，spark的conf也是在/etc/spark/conf。

此时，如上所述，将对应的hive-site.xml拷贝到spark/conf目录下即可

如果Hive的元数据存放在Mysql中，我们还需要准备好Mysql相关驱动，比如：mysql-connector-java-5.1.22-bin.jar。

2、编写测试代码

val conf=new SparkConf\(\).setAppName\("Spark-Hive"\).setMaster\("local"\)

val sc=new SparkContext\(conf\)

//create hivecontext

val sqlContext = new org.apache.spark.sql.hive.HiveContext\(sc\)

sqlContext.sql\("CREATE TABLE IF NOT EXISTS src \(key INT, value STRING\) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' "\)   //这里需要注意数据的间隔符

sqlContext.sql\("LOAD DATA INPATH '/user/liujiyu/spark/kv1.txt' INTO TABLE src  "\);

sqlContext.sql\(" SELECT \* FROM jn1"\).collect\(\).foreach\(println\)

sc.stop\(\)

3、下面列举一下出现的问题：

（1）如果没有将hive-site.xml拷贝到spark/conf目录下，会出现：

分析：从错误提示上面就知道，spark无法知道hive的元数据的位置，所以就无法实例化对应的client。

解决的办法就是必须将hive-site.xml拷贝到spark/conf目录下

（2）测试代码中没有加sc.stop会出现如下错误：

ERROR scheduler.LiveListenerBus: Listener EventLoggingListener threw an exception

java.lang.reflect.InvocationTargetException

在代码最后一行添加sc.stop\(\)解决了该问题。

