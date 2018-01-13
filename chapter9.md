# chapter9

spark on hive

ls: cannot access /home/hadoop/spark-2.2.0-bin-hadoop2.6/lib/spark-assembly-\*.jar: No such file or directory

出现这个问题的原因是

spark升级到spark2以后，原有lib目录下的大JAR包被分散成多个小JAR包，原来的spark-assembly-\*.jar已经不存在，所以hive没有办法找到这个JAR包。

解决方法

打开hive的安装目录下的bin目录，找到hive文件

cd $HIVE\_HOME/bin

vi hive

comment the following lines as follows

\# add Spark assembly jar to the classpath

\#if \[\[ -n "$SPARK\_HOME" \]\]

\#then

\#  sparkAssemblyPath=\`ls ${SPARK\_HOME}/lib/spark-assembly-\*.jar\`

\#  CLASSPATH="${CLASSPATH}:${sparkAssemblyPath}"

\#fi

Hive on Spark是Hive跑在Spark上，用的是Spark执行引擎，而不是MapReduce，和Hive on Tez的道理一样。

从Hive 1.1版本开始，Hive on Spark已经成为Hive代码的一部分了，并且在spark分支上面，可以看这里[https://github.com/apache/hive/tree/spark，并会定期的移到master分支上面去。关于Hive](https://github.com/apache/hive/tree/spark，并会定期的移到master分支上面去。关于Hive) on Spark的讨论和进度，可以看这里[https://issues.apache.org/jira/browse/HIVE-7292。](https://issues.apache.org/jira/browse/HIVE-7292。)

要想在Hive中使用Spark执行引擎，第一步当前就是环境设置，我们需要在Hive启动的时候加载spark-assembly-1.4.1-hadoop2.2.0.jar，最简单的方法是把spark-assembly-1.4.1-hadoop2.2.0.jar包直接拷贝到$HIVE\_HOME/lib目录下。

不过在Hive的官方文档上面还提供了两种加载Spark相关包的方法：

hive&gt; set spark.home=/location/to/sparkHome;

或者在启动Hive之前加上环境变量

export SPARK\_HOME=/home/iteblog/spark-1.4.1-bin-2.2.0

还有一点需要注意，Hive on Spark模式用到的Spark assembly包必须是没有用-phive参数编译的，否则出现以下异常：

15/08/28 11:00:32 ERROR yarn.ApplicationMaster: User class threw exception: java.lang.NoSuchFieldError: SPARK\_RPC\_CLIENT\_CONNECT\_TIMEOUT

目前Hive支持三种执行引擎：mr、tez、spark。因为我们需要使用Spark执行引擎，所以需要将hive.execution.engine设置为spark，具体如下：

hive&gt; set hive.execution.engine=spark;

其实，我们还可以在Hive中设置很多关于Spark的配置，诸如spark.eventLog.enabled、spark.executor.memory以及spark.executor.instances等。如下：

hive&gt; set spark.eventLog.enabled=true;

hive&gt; set spark.eventLog.dir=hdfs://iteblog/spark-jobs/eventLog;

hive&gt; set spark.executor.memory=4g;

hive&gt; set spark.executor.cores=2;

hive&gt; set spark.executor.instances=40;

hive&gt; set spark.serializer=org.apache.spark.serializer.KryoSerializer;

这样设置是不是很麻烦？我们可以在$HIVE\_HOME/conf里面创建spark-defaults.conf文件，然后加上需要设置的配置，最后设置到Hive启动的classpath环境中即可。当然，你完全可以将这些关于Spark的设置弄到hive-site.xml文件中，这样更方便。



官网地址：[https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started)

第一个坑：直接使用spark pre-build版本，下来后按照上面配置弄结果报错



java.lang.NoSuchFieldError: SPARK\_RPC\_CLIENT\_CONNECT\_TIMEOUT



    at org.apache.hive.spark.client.rpc.RpcConfiguration.&lt;clinit&gt;\(RpcConfiguration.java:46\)



报了rpc错误，这个坑要好查点，发现是因为需要自己编译spark因为预编译的带了hive的支持也就是-Phive，这个参数是spark-sql对hive的支持用的，结果用在hive on spark就不行。没事，我们开始编译吧。接下来苦逼日子来了。



第二个坑：版本不对，刚开始以为hive 能使用 spark的任何版本，结果发现错了，hive对spark版本有着严格要求，具体对应版本你可以下载hive源码里面，搜索他pom.xml文件里面的spark版本，如果版本不对，启动hive后会报错。具体错误如下：



Failed to execute spark task, with exception 'org.apache.hadoop.hive.ql.metadata.HiveException\(Failed to create spark client.\)' FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.spark.SparkTask



第三个坑：spark的编译，在这里我使用的是hive稳定版本2.01,查看他的pom.xml需要的spark版本是1.5.0.接着讲诉我遇到坑爹的事情，直接让我快一个星期时间白费。在编译的时候使用了hive 官网提供的命令，悲催的事情发生了，使用的命令是：



./make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.6"



然后将 lib/spark-assembly-1.5.0-hadoop2.6.0.jar 这个包拷到你现在的spark/lib 下另外一个相同的注意备份



但是当启动spark集群的时候报错了，错误如下



Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder



这个时候坑爹了，网上找来找去都没找到这个问题解决方案，上面错误原因就是说slf4j这个包找不到，你妹，完全按照官网来的，官网是怎么测试的呀，悲催了。接下来就是苦恼我一周的安装过程。



在网上找呀找，稍微看到一个靠谱的说法是编译的时候没有讲hadoop的jar包导进来，但是给出解决办法是在spark-env.sh里面添加



export SPARK\_DIST\_CLASSPATH=$\(hadoop classpath\)



好了添加了这个，spark master可以起来了，但是slaves仍然是上面错误，又开始找资料了。每天都在痛苦的阅读英文文档，差点抑郁了。而且国内google这不给力，用vpn时断时续，差点砸电脑。最后快绝望的时候回到spark官网，仔细阅读他提供的编译命令，死马当活马医吧。按照官网提供的编译命令再次编译spark.官网地址：http://spark.apache.org/docs/1.5.0/building-spark.html。他默认1.5.0使用是scala2.10进行编译，但是我安装的scala是2.11所以使用了以下命令。



./dev/change-scala-version.sh 2.11

mvn -Pyarn -Phadoop-2.4 -Dscala-2.11 -DskipTests clean package



这次编译的spark-assembly-1.5.0-hadoop2.6.0.jar 包有140mb左右比上次的多了40mb，感觉有点靠谱了，将这个包移过去，ok了！太开心了，一切都跑起来了。泪奔了，hive官网害人呀。



第四个坑，这个坑也算一个不大不小的坑，因为我偶然想装一下hue，在装hue遇到的错误，现在记录一下。

首先坑是hadoop的坑，如果在hadoop core-site.xml下面没有配置

&lt;property&gt;

    &lt;name&gt;hadoop.proxyuser.hdfs.hosts&lt;/name&gt;

    &lt;value&gt;\*&lt;/value&gt;

    &lt;description&gt;Defines the Unix user, hdfs, that will run the HttpFS server as

      a proxyuser.&lt;/description&gt;

  &lt;/property&gt;

  &lt;property&gt;

    &lt;name&gt;hadoop.proxyuser.hdfs.groups&lt;/name&gt;

    &lt;value&gt;\*&lt;/value&gt;

    &lt;description&gt;Replaces hdfs with the Unix user that will start the HttpFS server.

      &lt;/description&gt;

  &lt;/property&gt;

httpfs-site.xml

  &lt;property&gt;

    &lt;name&gt;hadoop.proxyuser.hue.hosts&lt;/name&gt;

    &lt;value&gt;\*&lt;/value&gt;

&lt;/property&gt;

&lt;property&gt;

    &lt;name&gt;hadoop.proxyuser.hue.groups&lt;/name&gt;

    &lt;value&gt;\*&lt;/value&gt;

&lt;/property&gt;

hdfs-site.xml

&lt;property&gt;

  &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;

  &lt;value&gt;true&lt;/value&gt;

&lt;/property&gt;

这三个配置没配好的你讲没有权限访问hadoop,但是这个都是小事情，网上教程多，不怎么算坑。

接下来hive配置才是一个不大不小的坑，因为网上基本没有什么资料，对应版本是hive是2.0.1

网上基本的版本都教了怎么连接，但是用网上连接方法hive.log老是显示 old version ? 我一直以为我hive版本过高了，但是换成1.2.1这个版本一样错误。

于是又开始看google，看的又快奔溃了。开始网上有人叫把hive.server2.authentication这个属性改为NOSASL,但是改来改去没啥卵用，好了我就不卖关子了，

hue要连接hive其实还需要修改两个属性

第一个属性是hive.server2.authentication 这个要是NOSASl

第二个属性是hive.server2.enable.doAs 一定要是flase 不然就会报 old version?

看到可视化界面的时候还是有点成就感的。



第五个坑，装好了那么多东西，肯定想跑跑试试，于是觉得对nginx 日志进行分析，这时候对网上hive 分析 nginx进行了搜索，搜了一大堆，但是按他们的正则建表都不可以用，又开始泪奔了。

不停百度，资料结果全是千篇一律，好像大家抄的都是一篇。结果还是找呀找，终于找到原因了

原来正则必须不是一根斜杠，原来java必须是两根\\把我建表sql展示一下，不然还是太空了。其中不需要out那一行，直接用input就行

create table logs\(ipaddress STRING,identity STRING,\`user\` STRING,time STRING,request STRING,status STRING,size STRING,host STRING,referer STRING,rbody STRING,agent STRING,forwarded STRING,uaddr STRING,ustatus STRING,urtime STRING,rtime STRING\)ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe' WITH SERDEPROPERTIES \('input.regex' = 

'\(\[0-9\]{1,3}\\.\[0-9\]{1,3}\\.\[0-9\]{1,3}\\.\[0-9\]{1,3}\) \(-\) \(-\) \(\\\[.\*\\\]\) \(\\"\[^\\"\]\*\\"\) \(\\d{3}\) \(\\d{1,}\) \(\\"\[^\\"\]\*\\"\) \(\\"\[^\\"\]\*\"\) \(-\|\[^\\s\]\*\) \(\\"\[^\\"\]\*\\"\) \(\\"\[^\\"\]\*\\"\) \(\[0-9\]{1,3}\\.\[0-9\]{1,3}\\.\[0-9\]{1,3}\\.\[0-9\]{1,3}:\[0-9\]{1,5}\) \(\\d{3}\) \(\[\\d\]{1,}\\.\[\\d\]{1,}\) \(\[\\d\]{1,}\\.\[\\d\]{1,}\)'\)

接下来可以跑了，扔了一个sql，你妹又报错了

return code 3 from org.apache.hadoop.hive.ql.exec.spark.SparkTask



这个坑不算大，因为通过查找hive,spark日志发现原来spark找不到org.apache.hadoop.hive.contrib.serde2.RegexSerDe

这个网上解决方法就多了，就是加上hive.aux.jars.path 这个属性里面你这个jar包地址如果是本地的记住前面要加file://哦不然找不到













