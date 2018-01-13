# chapter13

预先编译好的Spark assembly包是不支持Hive的，如果你需要在Spark中使用Hive，必须重新编译，加上&lt;code&gt;-Phive&lt;/code&gt;选项既可，具体如下：

\[iteblog@spark\]$ ./make-distribution.sh --tgz -Phadoop-2.2 -Pyarn -DskipTests -Dhadoop.version=2.2.0  -Phive

编译完成之后，会在SPARK\_HOME的lib目录下多产生三个jar包，分别是datanucleus-api-jdo-3.2.6.jar、datanucleus-core-3.2.10.jar、datanucleus-rdbms-3.2.9.jar，这些包都是Hive所需要的。下面就开始介绍步骤。



一、环境准备

为了让Spark能够连接到Hive的原有数据仓库，我们需要将Hive中的hive-site.xml文件拷贝到Spark的conf目录下，这样就可以通过这个配置文件找到Hive的元数据以及数据存放。

如果Hive的元数据存放在Mysql中，我们还需要准备好Mysql相关驱动，比如：mysql-connector-java-5.1.22-bin.jar。

二、启动spark-shell&lt;/h2&gt;

环境准备好之后，为了方便起见，我们使用spark-shell来进行说明如何通过Spark SQL读取Hive中的数据。我们可以通过下面的命令来启动spark-shell：

\[iteblog@spark\]$  bin/spark-shell --master yarn-client  --jars lib/mysql-connector-java-5.1.22-bin.jar 

....

15/08/27 18:21:25 INFO repl.SparkILoop: Created spark context..

Spark context available as sc.

....

15/08/27 18:21:30 INFO repl.SparkILoop: Created sql context \(with Hive support\)..

SQL context available as sqlContext.



启动spark-shell的时候会先向ResourceManager申请资源，而且还会初始化SparkContext和SQLContext实例。sqlContext对象其实是HiveContext的实例，sqlContext是进入Spark SQL的切入点。接下来我们来读取Hive中的数据。



scala&gt; sqlContext.sql\(&quot;CREATE EXTERNAL  TABLE IF NOT EXISTS ewaplog \(key STRING, value STRING\) 

STORED AS INPUTFORMAT 'com.hadoop.mapred.DeprecatedLzoTextInputFormat' OUTPUTFORMAT 

'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat' LOCATION '/user/iteblog/ewaplog' &quot;\)



res0: org.apache.spark.sql.DataFrame = \[result: string\]



scala&gt; sqlContext.sql\(&quot;LOAD DATA LOCAL INPATH '/data/test.lzo' INTO TABLE ewaplog&quot;\)

res1: org.apache.spark.sql.DataFrame = \[result: string\]



scala&gt; sqlContext.sql\(&quot;FROM ewaplog SELECT key, value&quot;\).collect\(\).foreach\(println\)



我们先创建了ewaplog表，然后导入数据，最后查询。我们可以看出所有的SQL和在Hive中是一样的，只是在Spark上运行而已。在执行SQL的时候，默认是调用hiveql解析器来解析SQL的。当然，你完全可以调用Spark SQL内置的SQL解析器sql，可以通过spark.sql.dialect

参数来设置。但是建议还是使用hivesql解析器，因为它支持的语法更多，而且还支持Hive的UDF函数，在多数情况下推荐使用hivesql解析器。



