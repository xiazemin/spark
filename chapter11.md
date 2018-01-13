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



Spark要很好的支持SQL，要完成解析\(parser\)、优化\(optimizer\)、执行\(execution\)三大过程。



spark cluster





处理顺序大致如下：



SQlParser生成LogicPlan Tree；

Analyzer和Optimizer将各种Rule作用于LogicalPlan Tree；

最终优化生成的LogicalPlan生成SparkRDD；

最后将生成的RDD交由Spark执行；





Spark SQL的两个组件

SQLContext：Spark SQL提供SQLContext封装Spark中的所有关系型功能。可以用之前的示例中的现有SparkContext创建SQLContext。

DataFrame：DataFrame是一个分布式的，按照命名列的形式组织的数据集合。DataFrame基于R语言中的data frame概念，与关系型数据库中的数据库表类似。通过调用将DataFrame的内容作为行RDD（RDD of Rows）返回的rdd方法，可以将DataFrame转换成RDD。可以通过如下数据源创建DataFrame：已有的RDD、结构化数据文件、JSON数据集、Hive表、外部数据库。





使用示例

编写简单的scala程序，从文本文件中加载用户数据并从数据集中创建一个DataFrame对象。然后运行DataFrame函数，执行特定的数据选择查询。



文本文件customers.txt中的内容如下：



Tom,12

Mike,13

Tony,34

Lili,8

David,21

Nike,18

Bush,29

Candy,42

编写Scala代码:



import org.apache.spark.\_



object Hello {



    // 创建一个表示用户的自定义类

    case class Person\(name: String, age: Int\)



    def main\(args: Array\[String\]\) {



        val conf = new SparkConf\(\).setAppName\("SparkSQL Demo"\)

        val sc = new SparkContext\(conf\)



        // 首先用已有的Spark Context对象创建SQLContext对象

        val sqlContext = new org.apache.spark.sql.SQLContext\(sc\)



        // 导入语句，可以隐式地将RDD转化成DataFrame

        import sqlContext.implicits.\_



        // 用数据集文本文件创建一个Person对象的DataFrame

        val people = sc.textFile\("/Users/urey/data/input2.txt"\).map\(\_.split\(","\)\).map\(p =&gt; Person\(p\(0\), p\(1\).trim.toInt\)\).toDF\(\)



        // 将DataFrame注册为一个表

        people.registerTempTable\("people"\)



        // SQL查询

        val teenagers = sqlContext.sql\("SELECT name, age FROM people WHERE age &gt;= 13 AND age &lt;= 19"\)



        // 输出查询结果，按照顺序访问结果行的各个列。

        teenagers.map\(t =&gt; "Name: " + t\(0\)\).collect\(\).foreach\(println\)



        sc.stop\(\)

        }

}

如上所示，Spark SQL提供了十分友好的SQL接口，可以与来自多种不同数据源的数据进行交互，而且所采用的语法也是团队熟知的SQL查询语法。这对于非技术类的项目成员，如数据分析师以及数据库管理员来说，非常实用。

