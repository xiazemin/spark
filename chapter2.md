# 运行样例



$ ./run-example org.apache.spark.examples.SparkPi

Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties

17/05/31 13:51:31 INFO SparkContext: Running Spark version 2.1.1

17/05/31 13:51:31 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

17/05/31 13:51:31 WARN Utils: Your hostname, localhost resolves to a loopback address: 127.0.0.1; using 172.24.26.76 instead \(on interface en0\)

17/05/31 13:51:31 WARN Utils: Set SPARK\_LOCAL\_IP if you need to bind to another address

17/05/31 13:51:31 INFO SecurityManager: Changing view acls to: didi

17/05/31 13:51:31 INFO SecurityManager: Changing modify acls to: didi

17/05/31 13:51:31 INFO SecurityManager: Changing view acls groups to:

17/05/31 13:51:31 INFO SecurityManager: Changing modify acls groups to:

17/05/31 13:51:31 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set\(didi\); groups with view permissions: Set\(\); users  with modify permissions: Set\(didi\); groups with modify permissions: Set\(\)

17/05/31 13:51:31 INFO Utils: Successfully started service 'sparkDriver' on port 51306.

17/05/31 13:51:31 INFO SparkEnv: Registering MapOutputTracker

17/05/31 13:51:32 INFO SparkEnv: Registering BlockManagerMaster

17/05/31 13:51:32 INFO BlockManagerMasterEndpoint: Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information

17/05/31 13:51:32 INFO BlockManagerMasterEndpoint: BlockManagerMasterEndpoint up

17/05/31 13:51:32 INFO DiskBlockManager: Created local directory at /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/blockmgr-23085ec1-55b0-45e6-8ced-00f14d9f8753

17/05/31 13:51:32 INFO MemoryStore: MemoryStore started with capacity 366.3 MB

17/05/31 13:51:32 INFO SparkEnv: Registering OutputCommitCoordinator

17/05/31 13:51:32 WARN Utils: Service 'SparkUI' could not bind on port 4040. Attempting port 4041.

17/05/31 13:51:32 INFO Utils: Successfully started service 'SparkUI' on port 4041.

17/05/31 13:51:32 INFO SparkUI: Bound SparkUI to 0.0.0.0, and started at http://172.24.26.76:4041

17/05/31 13:51:32 INFO SparkContext: Added JAR file:/Users/didi/spark/spark-2.1.1-bin-hadoop2.7/examples/jars/scopt\_2.11-3.3.0.jar at spark://172.24.26.76:51306/jars/scopt\_2.11-3.3.0.jar with timestamp 1496209892454

17/05/31 13:51:32 INFO SparkContext: Added JAR file:/Users/didi/spark/spark-2.1.1-bin-hadoop2.7/examples/jars/spark-examples\_2.11-2.1.1.jar at spark://172.24.26.76:51306/jars/spark-examples\_2.11-2.1.1.jar with timestamp 1496209892456

17/05/31 13:51:32 INFO Executor: Starting executor ID driver on host localhost

17/05/31 13:51:32 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 51307.

17/05/31 13:51:32 INFO NettyBlockTransferService: Server created on 172.24.26.76:51307

17/05/31 13:51:32 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy

17/05/31 13:51:32 INFO BlockManagerMaster: Registering BlockManager BlockManagerId\(driver, 172.24.26.76, 51307, None\)

17/05/31 13:51:32 INFO BlockManagerMasterEndpoint: Registering block manager 172.24.26.76:51307 with 366.3 MB RAM, BlockManagerId\(driver, 172.24.26.76, 51307, None\)

17/05/31 13:51:32 INFO BlockManagerMaster: Registered BlockManager BlockManagerId\(driver, 172.24.26.76, 51307, None\)

17/05/31 13:51:32 INFO BlockManager: Initialized BlockManager: BlockManagerId\(driver, 172.24.26.76, 51307, None\)

17/05/31 13:51:32 INFO SharedState: Warehouse path is 'file:/Users/didi/spark/spark-2.1.1-bin-hadoop2.7/bin/spark-warehouse'.

17/05/31 13:51:33 INFO SparkContext: Starting job: reduce at SparkPi.scala:38

17/05/31 13:51:33 INFO DAGScheduler: Got job 0 \(reduce at SparkPi.scala:38\) with 2 output partitions

17/05/31 13:51:33 INFO DAGScheduler: Final stage: ResultStage 0 \(reduce at SparkPi.scala:38\)

17/05/31 13:51:33 INFO DAGScheduler: Parents of final stage: List\(\)

17/05/31 13:51:33 INFO DAGScheduler: Missing parents: List\(\)

17/05/31 13:51:33 INFO DAGScheduler: Submitting ResultStage 0 \(MapPartitionsRDD\[1\] at map at SparkPi.scala:34\), which has no missing parents

17/05/31 13:51:33 INFO MemoryStore: Block broadcast\_0 stored as values in memory \(estimated size 1832.0 B, free 366.3 MB\)

17/05/31 13:51:33 INFO MemoryStore: Block broadcast\_0\_piece0 stored as bytes in memory \(estimated size 1167.0 B, free 366.3 MB\)

17/05/31 13:51:33 INFO BlockManagerInfo: Added broadcast\_0\_piece0 in memory on 172.24.26.76:51307 \(size: 1167.0 B, free: 366.3 MB\)

17/05/31 13:51:33 INFO SparkContext: Created broadcast 0 from broadcast at DAGScheduler.scala:996

17/05/31 13:51:33 INFO DAGScheduler: Submitting 2 missing tasks from ResultStage 0 \(MapPartitionsRDD\[1\] at map at SparkPi.scala:34\)

17/05/31 13:51:33 INFO TaskSchedulerImpl: Adding task set 0.0 with 2 tasks

17/05/31 13:51:34 INFO TaskSetManager: Starting task 0.0 in stage 0.0 \(TID 0, localhost, executor driver, partition 0, PROCESS\_LOCAL, 6086 bytes\)

17/05/31 13:51:34 INFO TaskSetManager: Starting task 1.0 in stage 0.0 \(TID 1, localhost, executor driver, partition 1, PROCESS\_LOCAL, 6086 bytes\)

17/05/31 13:51:34 INFO Executor: Running task 0.0 in stage 0.0 \(TID 0\)

17/05/31 13:51:34 INFO Executor: Running task 1.0 in stage 0.0 \(TID 1\)

17/05/31 13:51:34 INFO Executor: Fetching spark://172.24.26.76:51306/jars/spark-examples\_2.11-2.1.1.jar with timestamp 1496209892456

17/05/31 13:51:34 INFO TransportClientFactory: Successfully created connection to /172.24.26.76:51306 after 38 ms \(0 ms spent in bootstraps\)

17/05/31 13:51:34 INFO Utils: Fetching spark://172.24.26.76:51306/jars/spark-examples\_2.11-2.1.1.jar to /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-8bf0496b-196f-4726-a459-c61fd42ede02/userFiles-699993cd-878b-412e-bedb-5c043bd51e14/fetchFileTemp1832155739904729745.tmp

17/05/31 13:51:34 INFO Executor: Adding file:/private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-8bf0496b-196f-4726-a459-c61fd42ede02/userFiles-699993cd-878b-412e-bedb-5c043bd51e14/spark-examples\_2.11-2.1.1.jar to class loader

17/05/31 13:51:34 INFO Executor: Fetching spark://172.24.26.76:51306/jars/scopt\_2.11-3.3.0.jar with timestamp 1496209892454

17/05/31 13:51:34 INFO Utils: Fetching spark://172.24.26.76:51306/jars/scopt\_2.11-3.3.0.jar to /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-8bf0496b-196f-4726-a459-c61fd42ede02/userFiles-699993cd-878b-412e-bedb-5c043bd51e14/fetchFileTemp7331943545752929811.tmp

17/05/31 13:51:34 INFO Executor: Adding file:/private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-8bf0496b-196f-4726-a459-c61fd42ede02/userFiles-699993cd-878b-412e-bedb-5c043bd51e14/scopt\_2.11-3.3.0.jar to class loader

17/05/31 13:51:34 INFO Executor: Finished task 1.0 in stage 0.0 \(TID 1\). 1041 bytes result sent to driver

17/05/31 13:51:34 INFO Executor: Finished task 0.0 in stage 0.0 \(TID 0\). 1041 bytes result sent to driver

17/05/31 13:51:34 INFO TaskSetManager: Finished task 1.0 in stage 0.0 \(TID 1\) in 440 ms on localhost \(executor driver\) \(1/2\)

17/05/31 13:51:34 INFO TaskSetManager: Finished task 0.0 in stage 0.0 \(TID 0\) in 513 ms on localhost \(executor driver\) \(2/2\)

17/05/31 13:51:34 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool

17/05/31 13:51:34 INFO DAGScheduler: ResultStage 0 \(reduce at SparkPi.scala:38\) finished in 0.540 s

17/05/31 13:51:34 INFO DAGScheduler: Job 0 finished: reduce at SparkPi.scala:38, took 0.928114 s

Pi is roughly 3.144475722378612

17/05/31 13:51:34 INFO SparkUI: Stopped Spark web UI at http://172.24.26.76:4041

17/05/31 13:51:34 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!

17/05/31 13:51:34 INFO MemoryStore: MemoryStore cleared

17/05/31 13:51:34 INFO BlockManager: BlockManager stopped

17/05/31 13:51:34 INFO BlockManagerMaster: BlockManagerMaster stopped

17/05/31 13:51:34 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!

17/05/31 13:51:34 INFO SparkContext: Successfully stopped SparkContext

17/05/31 13:51:34 INFO ShutdownHookManager: Shutdown hook called

17/05/31 13:51:34 INFO ShutdownHookManager: Deleting directory /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-8bf0496b-196f-4726-a459-c61fd42ede02

