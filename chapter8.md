# chapter8

# Spark RDD、DataFrame和DataSet的区别

RDD



优点:



编译时类型安全 

编译时就能检查出类型错误

面向对象的编程风格 

直接通过类名点的方式来操作数据

缺点:



序列化和反序列化的性能开销 

无论是集群间的通信, 还是IO操作都需要对对象的结构和数据进行序列化和反序列化.

GC的性能开销 

频繁的创建和销毁对象, 势必会增加GC

import org.apache.spark.sql.SQLContext

import org.apache.spark.{SparkConf, SparkContext}



object Run {

  def main\(args: Array\[String\]\) {

    val conf = new SparkConf\(\).setAppName\("test"\).setMaster\("local"\)

    val sc = new SparkContext\(conf\)

    sc.setLogLevel\("WARN"\)

    val sqlContext = new SQLContext\(sc\)



    /\*\*

      \* id      age

      \* 1       30

      \* 2       29

      \* 3       21

      \*/

    case class Person\(id: Int, age: Int\)

    val idAgeRDDPerson = sc.parallelize\(Array\(Person\(1, 30\), Person\(2, 29\), Person\(3, 21\)\)\)



    // 优点1

    // idAge.filter\(\_.age &gt; ""\) // 编译时报错, int不能跟String比



    // 优点2

    idAgeRDDPerson.filter\(\_.age &gt; 25\) // 直接操作一个个的person对象

  }

}





DataFrame



DataFrame引入了schema和off-heap



schema : RDD每一行的数据, 结构都是一样的. 这个结构就存储在schema中. Spark通过schame就能够读懂数据, 因此在通信和IO时就只需要序列化和反序列化数据, 而结构的部分就可以省略了.



off-heap : 意味着JVM堆以外的内存, 这些内存直接受操作系统管理（而不是JVM）。Spark能够以二进制的形式序列化数据\(不包括结构\)到off-heap中, 当要操作数据时, 就直接操作off-heap内存. 由于Spark理解schema, 所以知道该如何操作.



off-heap就像地盘, schema就像地图, Spark有地图又有自己地盘了, 就可以自己说了算了, 不再受JVM的限制, 也就不再收GC的困扰了.



通过schema和off-heap, DataFrame解决了RDD的缺点, 但是却丢了RDD的优点. DataFrame不是类型安全的, API也不是面向对象风格的.



import org.apache.spark.sql.types.{DataTypes, StructField, StructType}

import org.apache.spark.sql.{Row, SQLContext}

import org.apache.spark.{SparkConf, SparkContext}



object Run {

  def main\(args: Array\[String\]\) {

    val conf = new SparkConf\(\).setAppName\("test"\).setMaster\("local"\)

    val sc = new SparkContext\(conf\)

    sc.setLogLevel\("WARN"\)

    val sqlContext = new SQLContext\(sc\)

    /\*\*

      \* id      age

      \* 1       30

      \* 2       29

      \* 3       21

      \*/

    val idAgeRDDRow = sc.parallelize\(Array\(Row\(1, 30\), Row\(2, 29\), Row\(4, 21\)\)\)



    val schema = StructType\(Array\(StructField\("id", DataTypes.IntegerType\), StructField\("age", DataTypes.IntegerType\)\)\)



    val idAgeDF = sqlContext.createDataFrame\(idAgeRDDRow, schema\)

    // API不是面向对象的

    idAgeDF.filter\(idAgeDF.col\("age"\) &gt; 25\) 

    // 不会报错, DataFrame不是编译时类型安全的

    idAgeDF.filter\(idAgeDF.col\("age"\) &gt; ""\) 

  }

}

DataSet



DataSet结合了RDD和DataFrame的优点, 并带来的一个新的概念Encoder



当序列化数据时, Encoder产生字节码与off-heap进行交互, 能够达到按需访问数据的效果, 而不用反序列化整个对象. Spark还没有提供自定义Encoder的API, 但是未来会加入.



下面看DataFrame和DataSet在2.0.0-preview中的实现



下面这段代码, 在1.6.x中创建的是DataFrame



// 上文DataFrame示例中提取出来的

val idAgeRDDRow = sc.parallelize\(Array\(Row\(1, 30\), Row\(2, 29\), Row\(4, 21\)\)\)



val schema = StructType\(Array\(StructField\("id", DataTypes.IntegerType\), StructField\("age", DataTypes.IntegerType\)\)\)



val idAgeDF = sqlContext.createDataFrame\(idAgeRDDRow, schema\)

1

2

3

4

5

6

但是同样的代码在2.0.0-preview中, 创建的虽然还叫DataFrame



// sqlContext.createDataFrame\(idAgeRDDRow, schema\) 方法的实现, 返回值依然是DataFrame

def createDataFrame\(rowRDD: RDD\[Row\], schema: StructType\): DataFrame = {

sparkSession.createDataFrame\(rowRDD, schema\)

}

但是其实却是DataSet, 因为DataFrame被声明为Dataset\[Row\]



package object sql {

  // ...省略了不相关的代码



  type DataFrame = Dataset\[Row\]

}

因此当我们从1.6.x迁移到2.0.0的时候, 无需任何修改就直接用上了DataSet.



下面是一段DataSet的示例代码



import org.apache.spark.sql.types.{DataTypes, StructField, StructType}

import org.apache.spark.sql.{Row, SQLContext}

import org.apache.spark.{SparkConf, SparkContext}



object Test {

  def main\(args: Array\[String\]\) {

    val conf = new SparkConf\(\).setAppName\("test"\).setMaster\("local"\) // 调试的时候一定不要用local\[\*\]

    val sc = new SparkContext\(conf\)

    val sqlContext = new SQLContext\(sc\)

    import sqlContext.implicits.\_



    val idAgeRDDRow = sc.parallelize\(Array\(Row\(1, 30\), Row\(2, 29\), Row\(4, 21\)\)\)



    val schema = StructType\(Array\(StructField\("id", DataTypes.IntegerType\), StructField\("age", DataTypes.IntegerType\)\)\)



    // 在2.0.0-preview中这行代码创建出的DataFrame, 其实是DataSet\[Row\]

    val idAgeDS = sqlContext.createDataFrame\(idAgeRDDRow, schema\)



    // 在2.0.0-preview中, 还不支持自定的Encoder, Row类型不行, 自定义的bean也不行

    // 官方文档也有写通过bean创建Dataset的例子，但是我运行时并不能成功

    // 所以目前需要用创建DataFrame的方法, 来创建DataSet\[Row\]

    // sqlContext.createDataset\(idAgeRDDRow\)



    // 目前支持String, Integer, Long等类型直接创建Dataset

    Seq\(1, 2, 3\).toDS\(\).show\(\)

    sqlContext.createDataset\(sc.parallelize\(Array\(1, 2, 3\)\)\).show\(\)

  }

}

参考



Introducing Apache Spark Datasets 

APACHE SPARK: RDD, DATAFRAME OR DATASET? 

RDD、DataFrame和DataSet的区别 

Spark 2.0.0-preview 官方文档

