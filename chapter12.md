# chapter12

spark sql是spark为处理结构化数据而产生的一个模块。同样也是为了适应习惯使用sql的使用者，方便用它操作数据。那这里就有一个问题？既然是sql语句，那么会像关系型数据库一样，去读取表的数据，然后用各种sql操作、组合数据吗？我们知道 mysql自然是读取mysql的表，oracle理所当然去oracle表去读取数据。那么spark sql应该去哪读取数据？spark sql是否也是需要先有表，然后再去读数据？

**spark sql去哪读取数据？**

如果你阅读过很多spark资料，那么这时候你就会想到spark有多个数据源。比如hive，Json，读取Parquet文件，文本文件，关系数据库等。那么该如何操作读取这些数据源，它有哪些函数？

**1.读取hive**

**  **spark sql包括sqlContext和hiveContext，hiveContext能读取hive数据库，所以hive是spark很重要的一个数据源，而且很多公司也是这么做的。

说明：

有一个非常重要的内容，就是spark2中SparkSession封装了这两个类.hiveContext在后面的版本将会被弃用，使用 SparkSession.builder.enableHiveSupport来替代。

