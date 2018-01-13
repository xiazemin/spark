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

