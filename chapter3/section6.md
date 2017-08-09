# scala  wordcount

p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 11.0px Monaco; color: \#ff2600}

错误: 找不到或无法加载主类 spark.WordCount

$    scala

Welcome to Scala 2.11.8 \(Java HotSpot\(TM\) 64-Bit Server VM, Java 1.8.0\_144\).

Type in expressions for evaluation. Or try :help.

右键－》scala－》set the scala installation －》Scala 2.11.8

17/08/09 10:04:36 WARN Utils: Service 'sparkDriver' could not bind on a random free port. You may check whether configuring an appropriate binding address.

17/08/09 10:04:36 WARN Utils: Service 'sparkDriver' could not bind on a random free port. You may check whether configuring an appropriate binding address.

17/08/09 10:04:36 ERROR SparkContext: Error initializing SparkContext.

java.net.BindException: Can't assign requested address: Service 'sparkDriver' failed after 16 retries \(on a random free port\)! Consider explicitly setting the appropriate binding address for the service 'sparkDriver' \(for example spark.driver.bindAddress for SparkDriver\) to the correct binding address.

```
at sun.nio.ch.Net.bind0\(Native Method\)
```

原因：SparkConf\(\).setMaster（"local［4］"），参数有问题

正确  
val  conf = new SparkConf\(\).setMaster\("local"\).setAppName\("CountingSheep"\).set\("spark.executor.memory", "1g"\)

输出：

Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties

17/08/09 17:25:03 INFO SparkContext: Running Spark version 2.2.0

17/08/09 17:25:04 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

17/08/09 17:25:04 WARN Utils: Your hostname, localhost resolves to a loopback address: 127.0.0.1; using 172.24.27.180 instead \(on interface en0\)

17/08/09 17:25:04 WARN Utils: Set SPARK\_LOCAL\_IP if you need to bind to another address

17/08/09 17:25:05 INFO SparkContext: Submitted application: CountingSheep

17/08/09 17:25:05 INFO SecurityManager: Changing view acls to: didi

17/08/09 17:25:05 INFO SecurityManager: Changing modify acls to: didi

17/08/09 17:25:05 INFO SecurityManager: Changing view acls groups to: 

17/08/09 17:25:05 INFO SecurityManager: Changing modify acls groups to: 

17/08/09 17:25:05 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set\(didi\); groups with view permissions: Set\(\); users  with modify permissions: Set\(didi\); groups with modify permissions: Set\(\)

17/08/09 17:25:05 INFO Utils: Successfully started service 'sparkDriver' on port 62411.

17/08/09 17:25:05 INFO SparkEnv: Registering MapOutputTracker

17/08/09 17:25:05 INFO SparkEnv: Registering BlockManagerMaster

17/08/09 17:25:05 INFO BlockManagerMasterEndpoint: Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information

17/08/09 17:25:05 INFO BlockManagerMasterEndpoint: BlockManagerMasterEndpoint up

17/08/09 17:25:05 INFO DiskBlockManager: Created local directory at /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/blockmgr-5edc7dd3-9324-451d-b6a6-7929a34072d5

17/08/09 17:25:05 INFO MemoryStore: MemoryStore started with capacity 912.3 MB

17/08/09 17:25:05 INFO SparkEnv: Registering OutputCommitCoordinator

17/08/09 17:25:05 INFO Utils: Successfully started service 'SparkUI' on port 4040.

17/08/09 17:25:05 INFO SparkUI: Bound SparkUI to 0.0.0.0, and started at http://172.24.27.180:4040

17/08/09 17:25:06 INFO Executor: Starting executor ID driver on host localhost

17/08/09 17:25:06 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 62412.

17/08/09 17:25:06 INFO NettyBlockTransferService: Server created on 172.24.27.180:62412

17/08/09 17:25:06 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy

17/08/09 17:25:06 INFO BlockManagerMaster: Registering BlockManager BlockManagerId\(driver, 172.24.27.180, 62412, None\)

17/08/09 17:25:06 INFO BlockManagerMasterEndpoint: Registering block manager 172.24.27.180:62412 with 912.3 MB RAM, BlockManagerId\(driver, 172.24.27.180, 62412, None\)

17/08/09 17:25:06 INFO BlockManagerMaster: Registered BlockManager BlockManagerId\(driver, 172.24.27.180, 62412, None\)

17/08/09 17:25:06 INFO BlockManager: Initialized BlockManager: BlockManagerId\(driver, 172.24.27.180, 62412, None\)

17/08/09 17:25:06 INFO MemoryStore: Block broadcast\_0 stored as values in memory \(estimated size 214.5 KB, free 912.1 MB\)

17/08/09 17:25:06 INFO MemoryStore: Block broadcast\_0\_piece0 stored as bytes in memory \(estimated size 20.4 KB, free 912.1 MB\)

17/08/09 17:25:06 INFO BlockManagerInfo: Added broadcast\_0\_piece0 in memory on 172.24.27.180:62412 \(size: 20.4 KB, free: 912.3 MB\)

17/08/09 17:25:06 INFO SparkContext: Created broadcast 0 from textFile at WordCount.scala:34



