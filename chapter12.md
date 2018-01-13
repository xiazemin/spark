# chapter12

spark sql是spark为处理结构化数据而产生的一个模块。同样也是为了适应习惯使用sql的使用者，方便用它操作数据。那这里就有一个问题？既然是sql语句，那么会像关系型数据库一样，去读取表的数据，然后用各种sql操作、组合数据吗？我们知道 mysql自然是读取mysql的表，oracle理所当然去oracle表去读取数据。那么spark sql应该去哪读取数据？spark sql是否也是需要先有表，然后再去读数据？

**spark sql去哪读取数据？**

如果你阅读过很多spark资料，那么这时候你就会想到spark有多个数据源。比如hive，Json，读取Parquet文件，文本文件，关系数据库等。那么该如何操作读取这些数据源，它有哪些函数？

**1.读取hive**

**  **spark sql包括sqlContext和hiveContext，hiveContext能读取hive数据库，所以hive是spark很重要的一个数据源，而且很多公司也是这么做的。

说明：

有一个非常重要的内容，就是spark2中SparkSession封装了这两个类.hiveContext在后面的版本将会被弃用，使用 SparkSession.builder.enableHiveSupport来替代。

如果要读取hive，还需要做一些配置，并且安装spark的客户端，也必须安装hive，这样spark才能找到依赖。配置文件 hive-site.xml, core-site.xml和hdfs-site.xml复制到conf目录下。如果没有配置 hive-site.xml，context会自动创建metastore\_db。

其实这是spark创建的默认本地Hive metastore。值得说明的是数据库的位置是由spark.sql.warehouse.dir配置属性指定的，默认是在$spark\_home/bin目录。spark2.0中 hive-site.xml的hive.metastore.warehouse.dir已经被弃用，使用spark.sql.warehouse.dir来指定数据仓库的位置。

我们可能遇到过在开发环境中，我们将hive-site.xml配置文件也复制到开发环境中，可是出现一种情况，在数据库中，只能找到default表，不能找到自己创建的表。这是因为我们并没有真正的连接hive server,而是使用的spark自己默认创建本地hive metastore。如果使用本地hive，我们所创建的表都会在spark的spark-warehouse中找到

在spark目录下,启动beeline,连接hive,可以设置访问密码,默认是没有的.

./bin/beeline

beeline&gt;!connect jdbc:hive2://fang-ubuntu:10000

Spark与Hive可以相互结合，同时Spark也可以使用DataFrame读取HBase里的数据，Hive也同样可以直接读取HBase的数据。只不过在Spark和Hive读取HBase数据的时候，需要做列簇或列映射，对于列不确定的需要列簇映射。 几种数据读取和分析思路



Hive on HBase做好表映射，然后使用Tez替换MR引擎，使用Hive做数据分析

这是最基本的一种方式，对于上层的数据分析可以提供基础支持，同时也可以通过写脚本的方式来做数据分析。但是跟别的数据源关联和分析比较麻烦，不像Spark可以提供多种数据源连接。

Spark SQL链接HBase，使用DataFrame做数据分析\(由Spark构建表映射\)

HuaweiBigData/astro，hortonworks-spark/shc，这是两个连接映射器，不过我目前没有去探索过。

Hive on HBase，开启Hive的thrift server，通过JDBC连接提取数据

这种方式与普通的JDBC取关系型数据库一样，可在SQL语句里传入Hive相应的处理函数，利用Hive来做计算然后提取结果。如果是Spark取数据的话，还不需要Spark做HBase表列的映射，很多函数可以使用Hive本身的。

但是，在目前Hive 1.2的JDBC版本里面，使用它会报错：java.sql.SQLException: Method not supported at org.apache.hive.jdbc.HiveResultSetMetaData.isSigned，这是因为在目前的JDBC版本里，甚至以后的Hive 2.0版本里，isSigned这个方法都没有做实现，并且在Spark 1.5及以上版本里，这个方法被Spark SQL的resolveTable所调用，所以在这些版本的Spark里，这种方式都无法使用，低版本的Spark或许可以。

Hive on HBase，Spark SQL使用HiveContext接触Hive的元数据信息，然后自己做数据提取和分析

HBase表列的映射不需要做，可以在Hive映射的基础上提取数据。

需要配置Spark识别Hive所映射的HBase表\(默认无法识别\)。

首先配置Spark在提交Job的时候加上以下jar包\(博主使用的HDP2.4.0.0-169\)： hive-hbase-handler-1.2.1000.2.4.0.0-169.jar hbase-client-1.1.2.2.4.0.0-169.jar hbase-protocol-1.1.2.2.4.0.0-169.jar hbase-common-1.1.2.2.4.0.0-169.jar hbase-server-1.1.2.2.4.0.0-169.jar htrace-core-3.1.0-incubating.jar guava-12.0.1.jar protobuf-java-2.5.0.jar 上述的jar包分别在Hive和HBase的lib目录下找。具体配置如下：在spark-defaults.conf的配置文件里加入spark.executor.extraClassPath和spark.driver.extraClassPath，其值是上述jar包的路径，多个jar包之间以:分割。如：spark.executor.extraClassPath /usr/hdp/2.4.0.0-169/spark/extrajars/hbase-client-1.1.2.2.4.0.0-169.jar:/usr/hdp/2.4.0.0-169/spark/extrajars/hbase-protocol-1.1.2.2.4.0.0-169.jar，在Ambari里配置就更简单了，在Custom spark-defaults里添加这两条配置即可。

其次，需将hbase-site.xml文件拷贝至HADOOP\_CONF\_DIR文件夹\(/etc/hadoop/conf\)下，因为在spark-env.sh里配置了HADOOP\_CONF\_DIR，里面的文件会被默认加载，所以hbase的配置也会被加载。其中有用的配置是以下几个： hbase.zookeeper.quorum zookeeper.znode.parent hbase.client.scanner.caching 其中caching这个值默认是100，为了性能可以适当提高，比如5000

Hive的各种函数和map, struct等数据结构在Spark里都可以找到对应的，处理数据更加简单。如下是取hive表里的struct类型字段，字段原型rowkey\_struct struct&lt;hash:string, resid:string, time\_stamp:string&gt;

val sqlContext = new HiveContext\(sc\)

sqlContext.table\("cbd.summary"\).select\("rowkey\_struct", "Amount"\).

select\($"rowkey\_struct".getField\("hash"\).as\("hash"\),$"rowkey\_struct".getField\("time\_stamp"\).as\("time\_stamp"\).cast\(LongType\), $"Amount".cast\(LongType\)\)

对于有些列名是动态变化的，比如存放外键关联字段的时候，并不知道列名和有多少个列。必须通过hive的map映射，映射的字段怎么处理呢？需要把map里的key-value键值对转换为行，然后将行转列，需要用到explode函数。如下是处理map类型字段，字段原型project\_info map&lt;String, string&gt;

case classFilterField\(EstiType: String, ProjectType: String\)

val sqlContext = new HiveContext\(sc\)

sqlContext.table\("cbd.project"\).select\("rowkey","project\_info"\).explode\($"project\_info"\) {

  case Row\(project\_info: Map\[String, String\]\) =&gt;

    val projectType = project\_info.filterKeys\(\_.contains\("工程类型"\)\).map\(\_.\_2\).headOption.getOrElse\("NaN"\) // 不知列名的采用匹配，已知列名的可以直接取

    Array\(FilterField\(project\_info.getOrElse\("计价方式", "NaN"\), projectType\)\)

}.select\("rowkey", "EstiType", "ProjectType"\)

同样的语句，执行速度比Hive on Tez快

在Ambari里，Spark默认已经配置好可以读取Hive的元数据信息

Hive on HBase，由Hive做好分析后，把数据写入到Hive其他的表里，然后Spark再从Hive表里取数据。 这种就比较绕，除非Hive里有的函数很重要但是Spark里不具备，可以这么干，否则直接使用Spark读取Hive的元数据信息更合适。

