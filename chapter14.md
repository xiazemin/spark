# chapter14

一.经验

1.Spark Streaming包含三种计算模式：nonstate .stateful .window



2.kafka可通过配置文件使用自带的zookeeper集群



3.Spark一切操作归根结底是对RDD的操作



4.部署Spark任务，不用拷贝整个架包，只需拷贝被修改的文件，然后在目标服务器上编译打包。



5.kafka的log.dirs不要设置成/tmp下的目录，貌似tmp目录有文件数和磁盘容量限制



6.ES的分片类似kafka的partition



7spark Graph根据边集合构建图，顶点集合只是指定图中哪些顶点有效



8.presto集群没必要采用on yarn模式，因为hadoop依赖HDFS，如果部分机器磁盘很小，hadoop会很尴尬，而presto是纯内存计算，不依赖磁盘，独立安装可以跨越多个集群，可以说有内存的地方就可以有presto



9.presto进程一旦启动，JVM server会一直占用内存



10.如果maven下载很慢，很可能是被天朝的GFW墙了，可以在maven安装目录的setting.conf配置文件mirrors标签下加入国内镜像抵制\*\*党的网络封锁，例如：



&lt;mirror&gt;

  &lt;id&gt;nexus-aliyun&lt;/id&gt;

  &lt;mirrorOf&gt;\*&lt;/mirrorOf&gt;

  &lt;name&gt;Nexus aliyun&lt;/name&gt;

  &lt;url&gt;http://maven.aliyun.com/nexus/content/groups/public&lt;/url&gt;

&lt;/mirror&gt;

11.编译spark，hive on spark就不要加-Phive参数，若需sparkSQL支持hive语法则要加-Phive参数



12.通过hive源文件pom.xml查看适配的spark版本，只要打版本保持一致就行，例如spark1.6.0和1.6.2都能匹配



13.打开Hive命令行客户端，观察输出日志是否有打印“SLF4J: Found binding in \[jar:file:/work/poa/hive-2.1.0-bin/lib/spark-assembly-1.6.2-hadoop2.6.0.jar!/org/slf4j/impl/StaticLoggerBinder.class\]”来判断hive有没有绑定spark



14.kafka的comsumer groupID对于spark direct streaming无效



15.shuffle write就是在一个stage结束计算之后，为了下一个stage可以执行shuffle类的算子，而将每个task处理的数据按key进行分类，将相同key都写入同一个磁盘文件中，而每一个磁盘文件都只属于下游stage的一个task，在将数据写入磁盘之前，会先将数据写入内存缓存中，下一个stage的task有多少个，当前stage的每个task就要创建多少份磁盘文件。



16.单个spark任务的excutor核数不宜设置过高，否则会导致其他JOB延迟



17.数据倾斜只发生在shuffle过程，可能触发shuffle操作的算子有：distinct, groupByKey, reduceByKey, aggregateByKey, join, cogroup, repartition等



18.运行时删除hadoop数据目录会导致依赖HDFS的JOB失效



19.sparkSQL UDAF中update函数的第二个参数 input: Row 对应的并非DataFrame的行，而是被inputSchema投影了的行



20.Spark的Driver只有在Action时才会收到结果



21.Spark需要全局聚合变量时应当使用累加器（Accumulator）



22.Kafka以topic与consumer group划分关系，一个topic的消息会被订阅它的消费者组全部消费，如果希望某个consumer使用topic的全部消息，可将该组只设一个消费者，每个组的消费者数目不能大于topic的partition总数，否则多出的consumer将无消可费



23.所有自定义类要实现serializable接口，否则在集群中无法生效



24.resources资源文件读取要在Spark Driver端进行，以局部变量方式传给闭包函数



25.DStream流转化只产生临时流对象，如果要继续使用，需要一个引用指向该临时流对象



26.提交到yarn cluster的作业不能直接print到控制台，要用log4j输出到日志文件中



27.HDFS文件路径写法为：hdfs://master:9000/文件路径，这里的master是namenode的hostname,9000是hdfs端口号。



28.不要随意格式化HDFS，这会带来数据版本不一致等诸多问题，格式化前要清空数据文件夹



29.搭建集群时要首先配置好主机名，并重启机器让配置的主机名生效



30.linux批量多机互信, 将pub秘钥配成一个



31小于128M的小文件都会占据一个128M的BLOCK，合并或者删除小文件节省磁盘空间



32.Non DFS Used指的是非HDFS的所有文件



33.spark两个分区方法coalesce和repartition，前者窄依赖，分区后数据不均匀，后者宽依赖，引发shuffle操作，分区后数据均匀



34.spark中数据写入ElasticSearch的操作必须在action中以RDD为单位执行



35.可以通过hive-site.xml修改spark.executor.instances, spark.executor.cores, spark.executor.memory等配置来优化hive on spark执行性能，不过最好配成动态资源分配。



















二.基本功能

0.常见问题:

1如果运行程序出现错误：Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/LoggerFactory，这是因为项目缺少slf4j-api.jar和slf4j-log4j12.jar这两个jar包导致的错误。

2如果运行程序出现错误：java.lang.NoClassDefFoundError: org/apache/log4j/LogManager，这是因为项目缺少log4j.jar这个jar包

3错误：Exception in thread "main" java.lang.NoSuchMethodError: org.slf4j.MDC.getCopyOfContextMap\(\)Ljava/util/Map，这是因为jar包版本冲突造成的。



1.配置spark-submit \(CDH版本\)

Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/fs/FSDataInputStream

        at org.apache.spark.deploy.SparkSubmitArguments.handleUnknown\(SparkSubmitArguments.scala:451\)

        at org.apache.spark.launcher.SparkSubmitOptionParser.parse\(SparkSubmitOptionParser.java:178\)

        at org.apache.spark.deploy.SparkSubmitArguments.&lt;init&gt;\(SparkSubmitArguments.scala:97\)

        at org.apache.spark.deploy.SparkSubmit$.main\(SparkSubmit.scala:113\)

        at org.apache.spark.deploy.SparkSubmit.main\(SparkSubmit.scala\)

Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.fs.FSDataInputStream

        at java.net.URLClassLoader$1.run\(URLClassLoader.java:366\)

        at java.net.URLClassLoader$1.run\(URLClassLoader.java:355\)

        at java.security.AccessController.doPrivileged\(Native Method\)

        at java.net.URLClassLoader.findClass\(URLClassLoader.java:354\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:425\)

        at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:308\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:358\)

        ... 5 more

解决方案:

在spark-env.sh文件中添加:



export SPARK\_DIST\_CLASSPATH=$\(hadoop classpath\)









2.启动spark-shell时,报错

INFO cluster.YarnClientSchedulerBackend: Registered executor: Actor\[akka.tcp://sparkExecutor@services07:34965/user/Executor\#1736210263\] with ID 1

INFO util.RackResolver: Resolved services07 to /default-rack

INFO storage.BlockManagerMasterActor: Registering block manager services07:51154 with 534.5 MB RAM

解决方案:

在spark的spark-env配置文件中配置下列配置项:



将export SPARK\_WORKER\_MEMORY, export SPARK\_DRIVER\_MEMORY, export SPARK\_YARN\_AM\_MEMORY的值设置成小于534.5 MB









3.启动spark SQL时,报错:

  Caused by: org.datanucleus.store.rdbms.connectionpool.DatastoreDriverNotFoundException: The specified datastore driver \("com.mysql.jdbc.Driver "\) was not found in the CLASSPATH. Please check your CLASSPATH specification, and the name of the driver.

解决方案:

在$SPARK\_HOME/conf/spark-env.sh文件中配置:



export SPARK\_CLASSPATH=$HIVE\_HOME/lib/mysql-connector-java-5.1.6-bin.jar









4.启动spark SQL时,报错:

  java.sql.SQLException: Access denied for user 'services02 '@'services02' \(using password: YES\)

解决方案:

检查hive-site.xml的配置项, 有以下这个配置项



&lt;property&gt;

    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;

    &lt;value&gt;123456&lt;/value&gt;

    &lt;description&gt;password to use against metastore database&lt;/description&gt;

&lt;/property&gt;

看该密码与与MySQL的登录密码是否一致











5.启动计算任务时报错:

报错信息为:



  org.apache.spark.rpc.RpcTimeoutException: Futures timed out after \[120 seconds\]. This timeout is controlled by spark.rpc.askTimeout

解决方案:

  分配的core不够, 多分配几核的CPU









6.启动计算任务时报错:

不断重复出现



  status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1     

  status.SparkJobMonitor: 2017-01-04 11:53:54,564    Stage-0\_0: 0\(+1\)/1     

  status.SparkJobMonitor: 2017-01-04 11:53:55,564    Stage-0\_0: 0\(+1\)/1     

  status.SparkJobMonitor: 2017-01-04 11:53:56,564    Stage-0\_0: 0\(+1\)/1             

解决方案:

    资源不够, 分配大点内存, 默认值为512MB.









7.启动Spark作为计算引擎时报错:

报错信息为:



java.io.IOException: Failed on local exception: java.nio.channels.ClosedByInterruptException; Host Details : local host is: "m1/192.168.179.201"; destination host is: "m1":9000; 

    at org.apache.hadoop.net.NetUtils.wrapException\(NetUtils.java:772\)

    at org.apache.hadoop.ipc.Client.call\(Client.java:1474\)

Caused by: java.nio.channels.ClosedByInterruptException

    at java.nio.channels.spi.AbstractInterruptibleChannel.end\(AbstractInterruptibleChannel.java:202\)

    at sun.nio.ch.SocketChannelImpl.connect\(SocketChannelImpl.java:681\)

17/01/06 11:01:43 INFO retry.RetryInvocationHandler: Exception while invoking getFileInfo of class ClientNamenodeProtocolTranslatorPB over m2/192.168.179.202:9000 after 9 fail over attempts. Trying to fail over immediately.

解决方案:

出现该问题的原因有多种, 我所遇到的是使用Hive On Spark时报了此错误,解决方案是:

在hive-site.xml文件下正确配置该项



&lt;property&gt;

    &lt;name&gt;spark.yarn.jar&lt;/name&gt;

    &lt;value&gt;hdfs://ns1/Jar/spark-assembly-1.6.0-hadoop2.6.0.jar&lt;/value&gt;

&lt;/property&gt;









8.启动spark集群时报错,启动命令为:start-mastersh

报错信息:



Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/Logger

        at java.lang.Class.getDeclaredMethods0\(Native Method\)

        at java.lang.Class.privateGetDeclaredMethods\(Class.java:2701\)

        at java.lang.Class.privateGetMethodRecursive\(Class.java:3048\)

        at java.lang.Class.getMethod0\(Class.java:3018\)

        at java.lang.Class.getMethod\(Class.java:1784\)

        at sun.launcher.LauncherHelper.validateMainClass\(LauncherHelper.java:544\)

        at sun.launcher.LauncherHelper.checkAndLoadMain\(LauncherHelper.java:526\)

Caused by: java.lang.ClassNotFoundException: org.slf4j.Logger

        at java.net.URLClassLoader.findClass\(URLClassLoader.java:381\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:424\)

        at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:331\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:357\)

        ... 7 more

解决方案:

将/home/centos/soft/hadoop/share/hadoop/common/lib目录下的slf4j-api-1.7.5.jar文件,slf4j-log4j12-1.7.5.jar文件和commons-logging-1.1.3.jar文件拷贝到/home/centos/soft/spark/lib目录下











9.启动spark集群时报错,启动命令为:start-mastersh

报错信息:



Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/conf/Configuration

        at java.lang.Class.getDeclaredMethods0\(Native Method\)

        at java.lang.Class.privateGetDeclaredMethods\(Class.java:2570\)

        at java.lang.Class.getMethod0\(Class.java:2813\)

        at java.lang.Class.getMethod\(Class.java:1663\)

        at sun.launcher.LauncherHelper.getMainMethod\(LauncherHelper.java:494\)

        at sun.launcher.LauncherHelper.checkAndLoadMain\(LauncherHelper.java:486\)

Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.conf.Configuration

        at java.net.URLClassLoader$1.run\(URLClassLoader.java:366\)

        at java.net.URLClassLoader$1.run\(URLClassLoader.java:355\)

        at java.security.AccessController.doPrivileged\(Native Method\)

        at java.net.URLClassLoader.findClass\(URLClassLoader.java:354\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:425\)

        at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:308\)

        at java.lang.ClassLoader.loadClass\(ClassLoader.java:358\)

        ... 6 more

解决方案:

官网资料:

    https://spark.apache.org/docs/latest/hadoop-provided.html\#apache-hadoop

编辑/home/centos/soft/spark/conf/spark-env.sh文件,配置下列配置项:



export SPARK\_DIST\_CLASSPATH=$\(/home/centos/soft/hadoop/bin/hadoop classpath\)









10.启动HPL/SQL存储过程时报错:

报错信息:



2017-01-10T15:20:18,491 ERROR \[HiveServer2-Background-Pool: Thread-97\] exec.TaskRunner: Error in executeTask

java.lang.OutOfMemoryError: PermGen space

        at java.lang.ClassLoader.defineClass1\(Native Method\)

        at java.lang.ClassLoader.defineClass\(ClassLoader.java:800\)

2017-01-10T15:20:18,491 ERROR \[HiveServer2-Background-Pool: Thread-97\] ql.Driver: FAILED: Execution Error, return code -101 from org.apache.hadoop.hive.ql.exec.spark.SparkTask. PermGen space

2017-01-10T15:20:18,491  INFO \[HiveServer2-Background-Pool: Thread-97\] ql.Driver: Completed executing command\(queryId=centos\_20170110152016\_240c1b5e-3153-4179-80af-9688fa7674dd\); Time taken: 2.113 seconds

2017-01-10T15:20:18,500 ERROR \[HiveServer2-Background-Pool: Thread-97\] operation.Operation: Error running hive query: 

org.apache.hive.service.cli.HiveSQLException: Error while processing statement: FAILED: Execution Error, return code -101 from org.apache.hadoop.hive.ql.exec.spark.SparkTask. PermGen space

        at org.apache.hive.service.cli.operation.Operation.toSQLException\(Operation.java:388\)

        at org.apache.hive.service.cli.operation.SQLOperation.runQuery\(SQLOperation.java:244\)

        at org.apache.hive.service.cli.operation.SQLOperation.access$800\(SQLOperation.java:91\)

Caused by: java.lang.OutOfMemoryError: PermGen space

        at java.lang.ClassLoader.defineClass1\(Native Method\)

        at java.lang.ClassLoader.defineClass\(ClassLoader.java:800\)

解决方案:

参考资料:

    http://blog.csdn.net/xiao\_jun\_0820/article/details/45038205

出现该问题是因为Spark默认使用全部资源, 而此时主机的内存已用, 应在Spark配置文件中限制内存的大小.

在hive-site.xml文件下配置该项:



&lt;property&gt;

    &lt;name&gt;spark.driver.extraJavaOptions&lt;/name&gt;

    &lt;value&gt;-XX:PermSize=128M -XX:MaxPermSize=512M&lt;/value&gt;

&lt;/property&gt;

或在spark-default.conf文件下配置:



spark.driver.extraJavaOptions             -XX:PermSize=128M -XX:MaxPermSize=256M

















三.Spark常见问题汇总

1.报错信息:

Operation category READ is not supported in state standbyorg.apache.hadoop.ipc.RemoteException\(org.apache.hadoop.ipc.StandbyException\): 

Operation category READ is not supported in state standby

解决方案:

查看执行Spark计算的是否处于standby状态, 用浏览器访问该主机:http://m1:50070, 如果处于standby状态, 则不可在处于StandBy机器运行spark计算，应切执行Spark计算的主机从Standby状态切换到Active状态











2.问题出现情景:

Spakr集群的所有运行数据在Master重启是都会丢失



解决方案:

配置spark.deploy.recoveryMode选项为ZOOKEEPER











3.报错信息:

由于Spark在计算的时候会将中间结果存储到/tmp目录，而目前linux又都支持tmpfs，其实就是将/tmp目录挂载到内存当中, 那么这里就存在一个问题，中间结果过多导致/tmp目录写满而出现如下错误



No Space Left on the device（Shuffle临时文件过多）

解决办法:

修改配置文件spark-env.sh,把临时文件引入到一个自定义的目录中去, 即:



export SPARK\_LOCAL\_DIRS=/home/utoken/datadir/spark/tmp









4.报错信息:

java.lang.OutOfMemory, unable to create new native thread

Caused by: java.lang.OutOfMemoryError: unable to create new native thread

        at java.lang.Thread.start0\(Native Method\)

        at java.lang.Thread.start\(Thread.java:640\)

解决方案:

上面这段错误提示的本质是Linux操作系统无法创建更多进程，导致出错，并不是系统的内存不足。因此要解决这个问题需要修改Linux允许创建更多的进程，就需要修改Linux最大进程数。

\(1\)修改Linux最大进程数



ulimit -a

\(2\)临时修改允许打开的最大进程数



ulimit -u 65535

\(3\)临时修改允许打开的文件句柄



ulimit -n 65535

\(4\)永久修改Linux最大进程数量



sudo vi /etc/security/limits.d/90-nproc.conf

\*          soft    nproc     60000

root       soft    nproc     unlimited

永久修改用户打开文件的最大句柄数，该值默认1024，一般都会不够，常见错误就是not open file

解决办法:



sudo vi /etc/security/limits.conf

bdata  soft    nofile  65536

bdata  hard    nofile  65536









5.问题出现情景:

Worker节点中的work目录占用许多磁盘空间, 这些是Driver上传到worker的文件, 会占用许多磁盘空间.



解决方案:

需要定时做手工清理. 目录地址：/home/centos/soft/spark/work











6.问题出现情景:

spark-shell提交Spark Application如何解决依赖库



解决方案:

利用--driver-class-path选项来指定所依赖的jar文件，注意的是--driver-class-path后如果需要跟着多个jar文件的话，jar文件之间使用冒号:来分割。











7.Spark在发布应用的时候，出现连接不上master

报错信息如下:



INFO AppClient$ClientEndpoint: Connecting to master spark://s1:7077...

WARN ReliableDeliverySupervisor: Association with remote system \[akka.tcp://sparkMaster@s1:7077\] has failed, address is now gated for \[5000\] ms. Reason: \[Disassociated\]

解决方案:

检查所有机器时间是否一致.hosts是否都配置了映射.客户端和服务器端的Scala版本是否一致.Scala版本是否和Spark兼容











8.开发spark应用程序（和Flume-NG结合时）发布应用时可能会报错

报错信息如下:



ERROR ReceiverSupervisorImpl: Stopped receiver with error: org.jboss.netty.channel.ChannelException: Failed to bind to: /192.168.10.156:18800

ERROR Executor: Exception in task 0.0 in stage 2.0 \(TID 70\)

                org.jboss.netty.channel.ChannelException: Failed to bind to: /192.168.10.156:18800

                at org.jboss.netty.bootstrap.ServerBootstrap.bind\(ServerBootstrap.java:272\)

Caused by: java.net.BindException: Cannot assign requested address

解决方案:

参考资料:

  http://www.tuicool.com/articles/Yfi2eyR

由于spark通过Master发布的时候，会自动选取发送到某一台的worker节点上，所以这里绑定端口的时候，需要选择相应的worker服务器，但是由于我们无法事先了解到，spark发布到哪一台服务器的，所以这里启动报错，是因为在192.168.10.156:18800的机器上面没有启动Driver程序，而是发布到了其他服务器去启动了，所以无法监听到该机器出现问题，所以我们需要设置spark分发包时，发布到所有worker节点机器，或者发布后，我们去寻找发布到了哪一台机器，重新修改绑定IP，重新发布，有一定几率发布成功。











9.使用Hive on Spark时报错:

ERROR XSDB6: Another instance of Derby may have already booted the database /home/bdata/data/metastore\_db.

解决方案:

在使用Hive on Spark模式操作hive里面的数据时，报以上错误，原因是因为HIVE采用了derby这个内嵌数据库作为数据库，它不支持多用户同时访问,解决办法就是把derby数据库换成mysql数据库即可











10.找不到hdfs集群名字dfscluster

报错信息:



  java.lang.IllegalArgumentException: java.net.UnknownHostException: dfscluster

解决办法：

将$HADOOP\_HOME/etc/hadoop/hdfs-site.xml文件拷贝到Spark集群的所有主机的$SPARK\_HOME/conf目录下，然后重启Spark集群



cd /home/centos/soft/spark/conf/

for i in {201,202,203}; 

do scp hdfs-site.xml 192.168.179.$i:/home/centos/soft/spark/conf/; 

done









11.在执行yarn集群或者客户端时，报错:

执行指令:



sh $SPARK\_HOME/bin/spark-sql --master yarn-client

报如下错误:



Exception in thread "main" java.lang.Exception: When running with master 'yarn-client' either HADOOP\_CONF\_DIR or YARN\_CONF\_DIR must be set in the environment.

解决办法:

根据提示，配置HADOOP\_CONF\_DIR or YARN\_CONF\_DIR的环境变量即可, 在spark-env.sh文件中配置以下几项:



export HADOOP\_HOME=/u01/hadoop-2.6.1

export HADOOP\_CONF\_DIR=$HADOOP\_HOME/etc/hadoop

PATH=$PATH:$HIVE\_HOME/bin:$HADOOP\_HOME/bin









12.提交spark计算任务时,报错:

报错信息如下:



Job aborted due to stage failure: Task 3 in stage 0.0 failed 4 times, most recent failure: Lost task 3.3 in

\[org.apache.spark.scheduler.TaskSchedulerImpl\]-\[ERROR\] Lost executor 0 on 192.168.10.38: remote Rpc client disassociated

\[org.apache.spark.scheduler.TaskSchedulerImpl\]-\[ERROR\] Lost executor 1 on 192.168.10.38: remote Rpc client disassociated

\[org.apache.spark.scheduler.TaskSchedulerImpl\]-\[ERROR\] Lost executor 2 on 192.168.10.38: remote Rpc client disassociated

\[org.apache.spark.scheduler.TaskSchedulerImpl\]-\[ERROR\] Lost executor 3 on 192.168.10.38: remote Rpc client disassociated

\[org.apache.spark.scheduler.TaskSetManager\]-\[ERROR\] Task 3 in stage 0.0 failed 4 times; aborting job

Exception in thread "main" org.apache.spark.SparkException : Job aborted due to stage failure: Task 3 in stage 0.0 failed 4 times, most recent failure: Lost task 3.3 in stage 0.0 \(TID 14, 192.168.10.38\): ExecutorLostFailure \(executor 3 lost\)

Driver stacktrace:

at org.apache.spark.scheduler.DAGScheduler.org$apache$spark$scheduler$DAGScheduler$$failJobAndIndependentStages\(DAGScheduler.scala:1283\)

解决方案:

这里遇到的问题主要是因为数据源数据量过大，而机器的内存无法满足需求，导致长时间执行超时断开的情况，数据无法有效进行交互计算，因此有必要增加内存











13.启动Spark计算任务:

长时间等待无反应，并且看到服务器上面的web界面有内存和核心数，但是没有分配，报错信息如下:



status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

status.SparkJobMonitor: 2017-01-04 11:53:51,564    Stage-0\_0: 0\(+1\)/1

日志信息显示：



WARN TaskSchedulerImpl: Initial job has not accepted any resources; check your cluster UI to ensure that workers are registered and have sufficient resources

解决方案:

出现上面的问题主要原因是因为我们通过参数spark.executor.memory设置的内存过大，已经超过了实际机器拥有的内存，故无法执行，需要等待机器拥有足够的内存后，才能执行任务，可以减少任务执行内存，设置小一些即可











14.内存不足或数据倾斜导致Executor Lost（spark-submit提交）

报错信息如下:



TaskSetManager: Lost task 1.0 in stage 6.0 \(TID 100, 192.168.10.37\): java.lang.OutOfMemoryError: Java heap space

INFO BlockManagerInfo: Added broadcast\_8\_piece0 in memory on 192.168.10.37:57139 \(size: 42.0 KB, free: 24.2 MB\)

INFO BlockManagerInfo: Added broadcast\_8\_piece0 in memory on 192.168.10.38:53816 \(size: 42.0 KB, free: 24.2 MB\)

INFO TaskSetManager: Starting task 3.0 in stage 6.0 \(TID 102, 192.168.10.37, ANY, 2152 bytes\)

WARN TaskSetManager: Lost task 1.0 in stage 6.0 \(TID 100, 192.168.10.37\): java.lang.OutOfMemoryError: Java heap space

            at java.io.BufferedOutputStream.&lt;init&gt;\(BufferedOutputStream.java:76\)

            at java.io.BufferedOutputStream.&lt;init&gt;\(BufferedOutputStream.java:59\)

            at org.apache.spark.sql.execution.UnsafeRowSerializerInstance$$anon$2.&lt;init&gt;\(UnsafeRowSerializer.scala:55\)

ERROR TaskSchedulerImpl: Lost executor 6 on 192.168.10.37: remote Rpc client disassociated

INFO TaskSetManager: Re-queueing tasks for 6 from TaskSet 6.0

WARN ReliableDeliverySupervisor: Association with remote system \[akka.tcp://sparkExecutor@192.168.10.37:42250\] has failed, address is now gated for \[5000\] ms. Reason: \[Disassociated\]

WARN TaskSetManager: Lost task 3.0 in stage 6.0 \(TID 102, 192.168.10.37\): ExecutorLostFailure \(executor 6 lost\)

INFO DAGScheduler: Executor lost: 6 \(epoch 8\)

INFO BlockManagerMasterEndpoint: Trying to remove executor 6 from BlockManagerMaster.

INFO BlockManagerMasterEndpoint: Removing block manager BlockManagerId\(6, 192.168.10.37, 57139\)

INFO BlockManagerMaster: Removed 6 successfully in removeExecutor

INFO AppClient$ClientEndpoint: Executor updated: app-20160115142128-0001/6 is now EXITED \(Command exited with code 52\)

INFO SparkDeploySchedulerBackend: Executor app-20160115142128-0001/6 removed: Command exited with code 52

INFO SparkDeploySchedulerBackend: Asked to remove non-existent executor 6

          org.apache.spark.SparkException: Job aborted due to stage failure: Task 0 in stage 6.0 failed 4 times, most recent failure: Lost task 0.3 in stage 6.0 \(TID 142, 192.168.10.36\): ExecutorLostFailure \(executor 4 lost\)

WARN TaskSetManager: Lost task 4.1 in stage 6.0 \(TID 137, 192.168.10.38\): java.lang.OutOfMemoryError: GC overhead limit exceeded

解决办法：

由于我们在执行Spark任务是，读取所需要的原数据，数据量太大，导致在Worker上面分配的任务执行数据时所需要的内存不够，直接导致内存溢出了，所以我们有必要增加Worker上面的内存来满足程序运行需要。

在Spark Streaming或者其他spark任务中，会遇到在Spark中常见的问题，典型如Executor Lost相关的问题\(shuffle fetch失败，Task失败重试等\)。这就意味着发生了内存不足或者数据倾斜的问题。这个目前需要考虑如下几个点以获得解决方案：



A.相同资源下，增加partition数可以减少内存问题。 原因如下：通过增加partition数，每个task要处理的数据少了，同一时间内，所有正在运行的task要处理的数量少了很多，所有Executor占用的内存也变小了。这可以缓解数据倾斜以及内存不足的压力。

B.关注shuffle read阶段的并行数。例如reduce, group 之类的函数，其实他们都有第二个参数，并行度\(partition数\)，只是大家一般都不设置。不过出了问题再设置一下，也不错。

C.给一个Executor核数设置的太多，也就意味着同一时刻，在该Executor的内存压力会更大，GC也会更频繁。我一般会控制在3个左右。然后通过提高Executor数量来保持资源的总量不变。











16. Spark Streaming 和kafka整合

报错信息如下：



  OffsetOutOfRangeException

解决方案：

如果和kafka消息中间件结合使用，请检查消息体是否大于默认设置1m，如果大于，则需要设置fetch.message.max.bytes=1m, 这里需要把值设置大些











17.报错信息:

java.io.IOException : Could not locate executable null\bin\winutils.exe in the Hadoop binaries.（spark sql on hive 任务引发HiveContext NullPointerException）



解决办法:

在开发hive和Spark整合的时候，如果是Windows系统，并且没有配置HADOOP\_HOME的环境变量，那么可能找不到winutils.exe这个工具，由于使用hive时，对该命令有依赖，所以不要忽视该错误，否则将无法创建HiveContext，一直报Exception in thread "main" java.lang.RuntimeException: java.lang.NullPointerException

因此，解决该办法有两个方式



方案A:

把任务打包成jar，上传到服务器上面，服务器是配置过HADOOP\_HOME环境变量的，并且不需要依赖winutils,所以只需要通过spark-submit方式提交即可，如：



  spark-submit --class com.pride.hive.HiveOnSparkTest --master spark://bdata4:7077 spark-simple-1.0.jar

方案B:

解决winutils.exe命令不可用问题，配置Windows上面HADOOP\_HOME的环境变量，或者在程序最开始的地方设置HADOOP\_HOME的属性配置,这里需要注意，由于最新版本已经没有winutils这些exe命令了，我们需要在其他地方下载该命令放入HADOOP的bin目录下，当然也可以直接配置下载项目的环境变量，变量名一定要是HADOOP\_HOME才行



下载地址: \(记得FQ哦\)

    https://github.com/srccodes/hadoop-common-2.2.0-bin/archive/master.zip

任何项目都生效，需要配置Windows的环境变量，如果只在程序中生效可在程序中配置即可，如:



//用于解决Windows下找不到winutils.exe命令

System. setProperty\("hadoop.home.dir", "E:\\Software\\hadoop-common-2.2.0-bin" \);









19.报错信息:

Exception in thread "main" org.apache.hadoop.security.AccessControlException : Permission denied: user=Administrator, access=WRITE, inode="/data":bdata:supergroup:drwxr-xr-x



解决办法

1.在系统的环境变量或JVM变量里面添加HADOOP\_USER\_NAME，如程序中添加:

System.setProperty\("HADOOP\_USER\_NAME", "bdata"\);, 这里的值就是以后会运行HADOOP上的Linux的用户名，如果是eclipse，则修改完重启eclipse，不然可能不生效

2.修改有问题的目录权限



hadoop fs -chmod 755 /tmp

并hive-site.xml文件中增加以下配置



&lt;property&gt;

    &lt;name&gt;hive.scratch.dir.permission&lt;/name&gt;

    &lt;value&gt;755&lt;/value&gt;

&lt;/property&gt;









20.运行Spark-SQL报错：

  org.apache.spark.sql.AnalysisException: unresolved operator 'Project

解决办法：

在Spark-sql和hive结合时或者单独Spark-sql，运行某些sql语句时，偶尔出现上面错误，那么我们可以检查一下sql的问题，这里遇到的问题是嵌套语句太多，导致spark无法解析，所以需要修改sql或者改用其他方式处理；特别注意该语句可能在hive里面没有错误，spark才会出现的一种错误。











21.报错信息如下:

org.apache.spark.SparkException: Only one SparkContext may be running in this JVM \(see SPARK-2243\). To ignore this error, set spark.driver.allowMultipleContexts = true.



解决方案：

使用Use this constructor JavaStreamingContext\(sparkContext: JavaSparkContext, batchDuration: Duration\) 替代 new JavaStreamingContext\(sparkConf, Durations.seconds\(5\)\)











22.报错信息如下:

java.lang.IllegalArgumentException: requirement failed: No output operations registered, so nothing to execute

解决方案：

tranformation最后一步产生的那个RDD必须有相应Action操作，例如massages.print\(\)等











23.报错信息如下:

ERROR ApplicationMaster: SparkContext did not initialize after waiting for 100000 ms. Please check earlier log output for errors. Failing the application



解决方案：

资源不能分配过大,或者没有把.setMaster\("local\[\*\]"\)去掉











24.报错信息如下:

java.util.regex.PatternSyntaxException: Dangling meta character '?' near index 0

解决方案：

元字符记得转义











25.报错信息如下:

Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/fs/FSDataInputStream

解决方案：

编译spark用了hadoop-provided参数，导致缺少hadoop相关包











26.报错信息如下:

org.apache.spark.SparkException: Task failed while writing rows Caused by: org.elasticsearch.hadoop.rest.EsHadoopInvalidRequest: null



解决方案：

ES负载过高，修复ES











27.报错信息如下:

org.apache.spark.SparkException: Task failed while writing rows scala.MatchError: Buffer\(10.113.80.29, None\) \(of class scala.collection.convert.Wrappers$JListWrapper\)



解决方案：

ES数据在sparksql类型转化时不兼容，可通过EsSpark.esJsonRDD以字符串形式取ES数据，再把rdd转换成dataframe











28.报错信息如下:

SparkListenerBus has already stopped! Dropping event SparkListenerStageCompleted

解决方案：

集群资源不够，确保真实剩余内存大于spark job申请的内存











29.报错信息如下:

ExecutorLostFailure \(executor 3 exited caused by one of the running tasks\) Reason: Container killed by YARN for exceeding memory limits. 61.0 GB of 61 GB physical memory used



解决方案：

配置项spark.storage.memoryFraction默认值为0.6, 应加大spark.storage.memoryFraction的系数











30.问题如下:

如何定位spark的数据倾斜 

解决方案：

在Spark Web UI看一下当前stage各个task分配的数据量以及执行时间，根据stage划分原理定位代码中shuffle类算子











31.报错信息如下:

如何解决spark数据倾斜 

解决方案：

过滤少数导致倾斜的key（仅限于抛弃的Key对作业影响很小）

提高shuffle操作并行度（提升效果有限）

两阶段聚合（局部聚合+全局聚合），先对相同的key加前缀变成多个key，局部shuffle后再去掉前缀，再次进行全局shuffle（仅适用于聚合类的shuffle操作，效果明显，对于join类的shuffle操作无效），

将reduce join转为map join，将小表进行广播，对大表map操作，遍历小表数据（仅适用于大小表或RDD情况）

使用随机前缀和扩容RDD进行join，对其中一个RDD每条数据打上n以内的随机前缀，用flatMap算子对另一个RDD进行n倍扩容并扩容后的每条数据依次打上0~n的前缀，最后将两个改造key后的RDD进行join（能大幅缓解join类型数据倾斜，需要消耗巨额内存）









32.报错信息如下:

org.apache.spark.SparkException: Failed to get broadcast\_790\_piece0 of broadcast\_790

解决方案：

删除spark-defaults.conf文件中spark.cleaner.ttl的配置











33.报错信息如下:

  MapperParsingException\[Malformed content, must start with an object

解决方案：

采用接口JavaEsSpark.saveJsonToEs，因为saveToEs只能处理对象不能处理字符串











34.报错信息如下:

  java.util.concurrent.TimeoutException: Cannot receive any reply in 120 seconds

解决方案：

确保所有节点之间能够免密码登录

确保所在的主机满足spark-env.sh中分配的CPU个数,若spark-env.sh中分配的CPU个数为一个,而master和worker在同一部主机上,则该主机需最少分配2个CPU









35.报错信息如下:

Exception in thread "main" org.apache.spark.SparkException: Yarn application has already ended! It might have been killed or unable to launch application master.



解决方案：

出现此类问题有很多种, 当时遇到这问题的因为是在spark未改动的情况下, 更换了Hive的版本导致版本不对出现了此问题, 解决此问题的方法是:



再次运行spark计算, 查看日志中Hive的版本, 检查当前Hive是否与Spark日志中的Hive版本一致

若Hive版本不一致, 则删除现有的Hive, 并删除MySQL中Hive的元数据\(若使用MySQL元数据库\), HDFS上hive, tmp, user目录下的数据

安装与Spark日志中版本匹配的Hive







