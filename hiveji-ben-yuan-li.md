1  Hive基本原理

Hadoop是一个流行的开源框架，用来存储和处理商用硬件上的大规模数据集。对于HDFS上的海量日志而言，编写Mapreduce程序代码对于类似数据仓库的需求来说总是显得相对于难以维护和重用，Hive作为一种基于Hadoop的数据仓库解决方案应运而生，并得到了广泛应用。

Hive是基于Hadoop的数据仓库平台，由Facebook贡献，其支持类似SQL的结构化查询功能。Facebook设计开发Hive的初衷就是让那些熟悉sql编程方式的人也可以更好的利用hadoop，hive可以让数据分析人员只关注于具体业务模型，而不需要深入了解Map/Reduce的编程细节，但是这并不意味着使用hive不需要了解和学习Map/Reduce编程模型和hadoop，复杂的业务需求和模型总是存在的，对于Hive分析人员来说，深入了解Hadoop和Hive的原理和Mapreduce模型，对于优化查询总有益处。

hive优点：成本低，可以通过类sql语句快速实现简单或复杂的MapReduce统计。



借助于Hadoop和HDFS的大数据存储能力，数据仍然存储于Hadoop的HDFS中，Hive提供了一种类SQL的查询语言：HiveQL（HQL），对数据进行管理和分析，开发人员可以近乎sql的方式来实现逻辑，从而加快应用开发效率。（关于Hadoop、hdfs的更多知识请参考hadoop官网及hadoop权威指南）



HQL经过解析和编译，最终会生成基于Hadoop平台的Map Reduce任务，Hadoop通过执行这些任务来完成HQL的执行。



 



1.1   Hive组件

Hive的组件总体上可以分为以下几个部分：用户接口（UI）、驱动、编译器、元数据（Hive系统参数数据）和执行引擎。

