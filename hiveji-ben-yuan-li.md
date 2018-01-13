1  Hive基本原理

Hadoop是一个流行的开源框架，用来存储和处理商用硬件上的大规模数据集。对于HDFS上的海量日志而言，编写Mapreduce程序代码对于类似数据仓库的需求来说总是显得相对于难以维护和重用，Hive作为一种基于Hadoop的数据仓库解决方案应运而生，并得到了广泛应用。

Hive是基于Hadoop的数据仓库平台，由Facebook贡献，其支持类似SQL的结构化查询功能。Facebook设计开发Hive的初衷就是让那些熟悉sql编程方式的人也可以更好的利用hadoop，hive可以让数据分析人员只关注于具体业务模型，而不需要深入了解Map/Reduce的编程细节，但是这并不意味着使用hive不需要了解和学习Map/Reduce编程模型和hadoop，复杂的业务需求和模型总是存在的，对于Hive分析人员来说，深入了解Hadoop和Hive的原理和Mapreduce模型，对于优化查询总有益处。

hive优点：成本低，可以通过类sql语句快速实现简单或复杂的MapReduce统计。

借助于Hadoop和HDFS的大数据存储能力，数据仍然存储于Hadoop的HDFS中，Hive提供了一种类SQL的查询语言：HiveQL（HQL），对数据进行管理和分析，开发人员可以近乎sql的方式来实现逻辑，从而加快应用开发效率。（关于Hadoop、hdfs的更多知识请参考hadoop官网及hadoop权威指南）

HQL经过解析和编译，最终会生成基于Hadoop平台的Map Reduce任务，Hadoop通过执行这些任务来完成HQL的执行。

1.1   Hive组件

Hive的组件总体上可以分为以下几个部分：用户接口（UI）、驱动、编译器、元数据（Hive系统参数数据）和执行引擎。

据库对表数据进行写时严重不同，Hive对数据的验证方式为读时模式，即只有在读表数据的时候，hive才检查解析具体的字段、shema等，从而保证了大数据量的快速加载。

既然hive采用的读时验证机制，那么 如果表schema与表文件内容不匹配，会发生什么呢？

答案是hive会尽其所能的去读数据。如果schema中表有10个字段，而文件记录却只有3个字段，那么其中7个字段将为null；如果某些字段类型定位为数值类型，但是记录中却为非数值字符串，这些字段也将会被转换为null。简而言之，hive会努力catch读数据时遇到的错误，并努力返回。既然Hive表数据存储在HDFS中且Hive采用的是读时验证方式，定义完表的schema会自动生成表数据的HDFS目录，且我们可以以任何可能的方式来加载表数据或者利用HDFS API将数据写入文件，同理，当我们若需要将hive数据写入其他库（如oracle），也可以直接通过api读取数据再写入目标库。在实际生产环境中，当需要数据仓库之间的迁移时，就可以直接利用api将源库的数据直接写入hive库的表文件中，包括淘宝开源的datax数据交换系统都采用类似的方式来交换跨库数据。

再次注意，加载或者写入的数据内容要和表定义的schema一致，否则将会造成字段或者表为空。

1.2   Hive数据模型

从数据仓库的角度看，Hive是建立在Hadoop上的数据仓库基础架构，可以方便的ETL操作。Hive没有专门的数据存储格式，也没有为数据建立索引，用于可以非常自由的组织Hive中的表，只需要在创建表的时候定义好表的schema即可。Hive中包含4中数据模型：Tabel、ExternalTable、Partition、Bucket。

a\)         Table：类似与传统数据库中的Table，每一个Table在Hive中都有一个相应的目录来存储数据。例如：一个表t，它在HDFS中的路径为：/user/hive/warehouse/t。

b\)        Partition：类似于传统数据库中划分列的索引。在Hive中，表中的一个Partition对应于表下的一个目录，所有的Partition数据都存储在对应的目录中。例如：t表中包含ds和city两个Partition，则对应于ds=2014，city=beijing的HDFS子目录为：/user/hive/warehouse/t/ds=2014/city=Beijing；

需要注意的是，分区列是表的伪列，表数据文件中并不存在这个分区列的数据。

c\)         Buckets：对指定列计算的hash，根据hash值切分数据，目的是为了便于并行，每一个Buckets对应一个文件。将user列分数至32个Bucket上，首先对user列的值计算hash，比如，对应hash=0的HDFS目录为：/user/hive/warehouse/t/ds=2014/city=Beijing/part-00000；对应hash=20的目录为：/user/hive/warehouse/t/ds=2014/city=Beijing/part-00020。

d\)        External Table指向已存在HDFS中的数据，可创建Partition。Managed Table创建和数据加载过程，可以用统一语句实现，实际数据被转移到数据仓库目录中，之后对数据的访问将会直接在数据仓库的目录中完成。删除表时，表中的数据和元数据都会删除。ExternalTable只有一个过程，因为加载数据和创建表是同时完成。数据是存储在Location后面指定的HDFS路径中的，并不会移动到数据仓库中。

1.3   Hive翻译成MapReduce Job

Hive编译器将HQL代码转换成一组操作符（operator），操作符是Hive的最小操作单元，每个操作符代表了一种HDFS操作或者MapReduce作业。Hive中的操作符包括：

1.4   与一般SQL的区别

Hive 视图与一般数据库视图

Hive视图与一般数据库视图作用角色相同，都是基于数据规模缩减或者基于安全机制下的某些条件查询下的数据子集。Hive视图只支持逻辑视图，不支持物化视图，即每次对视图的查询hive都将执行查询任务，因此视图不会带来性能上的提升。作为Hive查询优化的一部分，对视图的查询条件语句和视图的定义查询条件语句将会尽可能的合并成一个条件查询。

Hive索引与一般数据库索引

相比于传统数据库，Hive只提供有限的索引功能，通过在某些字段上建立索引来加速某些操作。通常当逻辑分区太多太细，partition无法满足时，可以考虑建立索引。Hive1.2.1版本目前支持的索引类型有CompactIndexHandler和Bitmap。

CompactIndexHandler压缩索引通过将列中相同的值得字段进行压缩从而减小存储和加快访问时间。需要注意的是Hive创建压缩索引时会将索引数据也存储在Hive表中。对于表tb\_index \(id int, name string\)而言，建立索引后的索引表中默认的三列一次为索引列（id）、hdfs文件地址\(\_bucketname\)、偏移量\(offset\)。特别注意，offset列类型为array&lt;bigint&gt;。

Bitmap位图索引作为一种常见的索引，如果索引列只有固定的几个值，那么就可以采用位图索引来加速查询。利用位图索引可以方便的进行AND/OR/XOR等各类计算，Hive0.8版本开始引入位图索引，位图索引在大数据处理方面的应用广泛，比如可以利用bitmap来计算用户留存率（索引做与运算，效率远好于join的方式）。如果Bitmap索引很稀疏，那么就需要对索引压缩以节省存储空间和加快IO。Hive的Bitmap Handler采用的是EWAH（[https://github.com/lemire/javaewah）压缩方式。](https://github.com/lemire/javaewah）压缩方式。)

从Hive索引功能来看，其主要功能就是避免第一轮mr任务的全表扫描，而改为扫描索引表。如果索引索引表本身很大，其开销仍然很大，在集群资源充足的情况下，可以忽略使用hive下的索引。



 



2     Schema设计

没有通用的schema，只有合适的schema。在设计Hive的schema的时候，需要考虑到存储、业务上的高频查询造成的开销等等，设计适合自己的数据模型。



 



2.1   设置分区表

对于Hive来说，利用分区来设计表总是必要的，分区提供了一种隔离数据和优化查询的便利的方式。特别是面对日益增长的数据规模。设置符合逻辑的分区可以避免进行全表扫描，只需加载特定某些hdfs目录的数据文件。



设置分区时，需要考虑被设置成分区的字段，按照时间分区一般而言就是一个好的方案，其好处在于其是按照不同时间粒度来确定合适大小的数据积累量，随着时间的推移，分区数量的增长是均匀的，分区的大小也是均匀的。达观数据每日处理大量的用户日志，对于user\_log来说，设置分区字段为日期（天）是合理的。如果以userid字段来建立动态分区，而userid的基数是非常大的，显然分区数目是会超过hive的默认设置而执行失败。如果相对userid进行hash，我们可以以userid进行分桶（bucket），根据userid进行hash然后分发到桶中，相同hash值的userid会分发到同一个桶中。每个桶对应着一个单独的文件。



 



2.2   避免小文件

虽然分区有利于隔离数据和查询，设置过多过细的分区也会带来瓶颈，主要是因为HDFS非常容易存储大数据文件，由于分区对应着hdfs的目录结构，当存在过多的分区时，意味着文件的数目就越多，过多增长的小文件会给namecode带来巨大的性能压力。同时小文件过多会影响JOB的执行，hadoop会将一个job转换成多个task，即使对于每个小文件也需要一个task去单独处理，task作为一个独立的jvm实例，其开启和停止的开销可能会大大超过实际的任务处理时间。因此，hive表设计的分区不应该过多过细，每个目录下的文件足够大，应该是文件系统中块大小的若干倍。



 



查询避免生成小文件



既然hive或者说hadoop需要大文件，HQL执行语句也需要注意输入文件和输出文件的大小，防止生成过多小文件。hive可以通过配置参数在mr过程中合并小文件。

