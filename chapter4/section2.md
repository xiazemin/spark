# 读本地文件

package spark

import org.apache.spark.\_

import SparkContext.\_

object WordCount {

def main\(args: Array\[String\]\) {

```
 val conf = new SparkConf\(\)

         .setMaster\("local"\)

         .setAppName\("CountingSheep"\)

         .set\("spark.executor.memory", "1g"\)

 val sc = new SparkContext\(conf\)     
```

val file=sc.textFile\("file:///Users/didi/java/WordCount/input/\*.txt"\)

```
 val count=file.flatMap\(line=&gt;line.split\(" "\)\).map\(word=&gt;\(word,1\)\).reduceByKey\(\_+\_\)

count.collect.foreach\(println\)

 sc.stop\(\)
```

}

}



Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties

17/08/09 17:51:14 INFO SparkContext: Running Spark version 2.2.0

17/08/09 17:51:15 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

17/08/09 17:51:15 WARN Utils: Your hostname, didideMacBook-Air-42.local resolves to a loopback address: 127.0.0.1, but we couldn't find any external IP address!

17/08/09 17:51:15 WARN Utils: Set SPARK\_LOCAL\_IP if you need to bind to another address

17/08/09 17:51:15 INFO SparkContext: Submitted application: CountingSheep

17/08/09 17:51:15 INFO SecurityManager: Changing view acls to: didi

17/08/09 17:51:15 INFO SecurityManager: Changing modify acls to: didi

17/08/09 17:51:15 INFO SecurityManager: Changing view acls groups to: 

17/08/09 17:51:15 INFO SecurityManager: Changing modify acls groups to: 

17/08/09 17:51:15 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set\(didi\); groups with view permissions: Set\(\); users  with modify permissions: Set\(didi\); groups with modify permissions: Set\(\)

17/08/09 17:51:15 INFO Utils: Successfully started service 'sparkDriver' on port 63624.

17/08/09 17:51:16 INFO SparkEnv: Registering MapOutputTracker

17/08/09 17:51:16 INFO SparkEnv: Registering BlockManagerMaster

17/08/09 17:51:16 INFO BlockManagerMasterEndpoint: Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information

17/08/09 17:51:16 INFO BlockManagerMasterEndpoint: BlockManagerMasterEndpoint up

17/08/09 17:51:16 INFO DiskBlockManager: Created local directory at /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/blockmgr-6d9c9763-6759-4a92-a3cd-43e0cab56234

17/08/09 17:51:16 INFO MemoryStore: MemoryStore started with capacity 912.3 MB

17/08/09 17:51:16 INFO SparkEnv: Registering OutputCommitCoordinator

17/08/09 17:51:16 INFO Utils: Successfully started service 'SparkUI' on port 4040.

17/08/09 17:51:16 INFO SparkUI: Bound SparkUI to 0.0.0.0, and started at http://127.0.0.1:4040

17/08/09 17:51:16 INFO Executor: Starting executor ID driver on host localhost

17/08/09 17:51:16 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 63625.

17/08/09 17:51:16 INFO NettyBlockTransferService: Server created on 127.0.0.1:63625

17/08/09 17:51:16 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy

17/08/09 17:51:16 INFO BlockManagerMaster: Registering BlockManager BlockManagerId\(driver, 127.0.0.1, 63625, None\)

17/08/09 17:51:16 INFO BlockManagerMasterEndpoint: Registering block manager 127.0.0.1:63625 with 912.3 MB RAM, BlockManagerId\(driver, 127.0.0.1, 63625, None\)

17/08/09 17:51:16 INFO BlockManagerMaster: Registered BlockManager BlockManagerId\(driver, 127.0.0.1, 63625, None\)

17/08/09 17:51:16 INFO BlockManager: Initialized BlockManager: BlockManagerId\(driver, 127.0.0.1, 63625, None\)

17/08/09 17:51:17 INFO MemoryStore: Block broadcast\_0 stored as values in memory \(estimated size 214.5 KB, free 912.1 MB\)

17/08/09 17:51:17 INFO MemoryStore: Block broadcast\_0\_piece0 stored as bytes in memory \(estimated size 20.4 KB, free 912.1 MB\)

17/08/09 17:51:17 INFO BlockManagerInfo: Added broadcast\_0\_piece0 in memory on 127.0.0.1:63625 \(size: 20.4 KB, free: 912.3 MB\)

17/08/09 17:51:17 INFO SparkContext: Created broadcast 0 from textFile at WordCount.scala:35

17/08/09 17:51:17 INFO FileInputFormat: Total input paths to process : 2

17/08/09 17:51:17 INFO SparkContext: Starting job: collect at WordCount.scala:37

17/08/09 17:51:18 INFO DAGScheduler: Registering RDD 3 \(map at WordCount.scala:36\)

17/08/09 17:51:18 INFO DAGScheduler: Got job 0 \(collect at WordCount.scala:37\) with 2 output partitions

17/08/09 17:51:18 INFO DAGScheduler: Final stage: ResultStage 1 \(collect at WordCount.scala:37\)

17/08/09 17:51:18 INFO DAGScheduler: Parents of final stage: List\(ShuffleMapStage 0\)

17/08/09 17:51:18 INFO DAGScheduler: Missing parents: List\(ShuffleMapStage 0\)

17/08/09 17:51:18 INFO DAGScheduler: Submitting ShuffleMapStage 0 \(MapPartitionsRDD\[3\] at map at WordCount.scala:36\), which has no missing parents

17/08/09 17:51:18 INFO MemoryStore: Block broadcast\_1 stored as values in memory \(estimated size 4.7 KB, free 912.1 MB\)

17/08/09 17:51:18 INFO MemoryStore: Block broadcast\_1\_piece0 stored as bytes in memory \(estimated size 2.8 KB, free 912.1 MB\)

17/08/09 17:51:18 INFO BlockManagerInfo: Added broadcast\_1\_piece0 in memory on 127.0.0.1:63625 \(size: 2.8 KB, free: 912.3 MB\)

17/08/09 17:51:18 INFO SparkContext: Created broadcast 1 from broadcast at DAGScheduler.scala:1006

17/08/09 17:51:18 INFO DAGScheduler: Submitting 2 missing tasks from ShuffleMapStage 0 \(MapPartitionsRDD\[3\] at map at WordCount.scala:36\) \(first 15 tasks are for partitions Vector\(0, 1\)\)

17/08/09 17:51:18 INFO TaskSchedulerImpl: Adding task set 0.0 with 2 tasks

17/08/09 17:51:18 INFO TaskSetManager: Starting task 0.0 in stage 0.0 \(TID 0, localhost, executor driver, partition 0, PROCESS\_LOCAL, 4856 bytes\)

17/08/09 17:51:18 INFO Executor: Running task 0.0 in stage 0.0 \(TID 0\)

17/08/09 17:51:18 INFO HadoopRDD: Input split: file:/Users/didi/java/WordCount/input/input1.txt:0+12

17/08/09 17:51:18 INFO Executor: Finished task 0.0 in stage 0.0 \(TID 0\). 1151 bytes result sent to driver

17/08/09 17:51:18 INFO TaskSetManager: Starting task 1.0 in stage 0.0 \(TID 1, localhost, executor driver, partition 1, PROCESS\_LOCAL, 4856 bytes\)

17/08/09 17:51:18 INFO Executor: Running task 1.0 in stage 0.0 \(TID 1\)

17/08/09 17:51:18 INFO TaskSetManager: Finished task 0.0 in stage 0.0 \(TID 0\) in 264 ms on localhost \(executor driver\) \(1/2\)

17/08/09 17:51:18 INFO HadoopRDD: Input split: file:/Users/didi/java/WordCount/input/input2.txt:0+13

17/08/09 17:51:18 INFO Executor: Finished task 1.0 in stage 0.0 \(TID 1\). 1108 bytes result sent to driver

17/08/09 17:51:18 INFO TaskSetManager: Finished task 1.0 in stage 0.0 \(TID 1\) in 49 ms on localhost \(executor driver\) \(2/2\)

17/08/09 17:51:18 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool 

17/08/09 17:51:18 INFO DAGScheduler: ShuffleMapStage 0 \(map at WordCount.scala:36\) finished in 0.325 s

17/08/09 17:51:18 INFO DAGScheduler: looking for newly runnable stages

17/08/09 17:51:18 INFO DAGScheduler: running: Set\(\)

17/08/09 17:51:18 INFO DAGScheduler: waiting: Set\(ResultStage 1\)

17/08/09 17:51:18 INFO DAGScheduler: failed: Set\(\)

17/08/09 17:51:18 INFO DAGScheduler: Submitting ResultStage 1 \(ShuffledRDD\[4\] at reduceByKey at WordCount.scala:36\), which has no missing parents

17/08/09 17:51:18 INFO MemoryStore: Block broadcast\_2 stored as values in memory \(estimated size 3.2 KB, free 912.1 MB\)

17/08/09 17:51:18 INFO MemoryStore: Block broadcast\_2\_piece0 stored as bytes in memory \(estimated size 1961.0 B, free 912.1 MB\)

17/08/09 17:51:18 INFO BlockManagerInfo: Added broadcast\_2\_piece0 in memory on 127.0.0.1:63625 \(size: 1961.0 B, free: 912.3 MB\)

17/08/09 17:51:18 INFO SparkContext: Created broadcast 2 from broadcast at DAGScheduler.scala:1006

17/08/09 17:51:18 INFO DAGScheduler: Submitting 2 missing tasks from ResultStage 1 \(ShuffledRDD\[4\] at reduceByKey at WordCount.scala:36\) \(first 15 tasks are for partitions Vector\(0, 1\)\)

17/08/09 17:51:18 INFO TaskSchedulerImpl: Adding task set 1.0 with 2 tasks

17/08/09 17:51:18 INFO TaskSetManager: Starting task 0.0 in stage 1.0 \(TID 2, localhost, executor driver, partition 0, ANY, 4621 bytes\)

17/08/09 17:51:18 INFO Executor: Running task 0.0 in stage 1.0 \(TID 2\)

17/08/09 17:51:18 INFO ShuffleBlockFetcherIterator: Getting 2 non-empty blocks out of 2 blocks

17/08/09 17:51:18 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 11 ms

17/08/09 17:51:18 INFO Executor: Finished task 0.0 in stage 1.0 \(TID 2\). 1265 bytes result sent to driver

17/08/09 17:51:18 INFO TaskSetManager: Starting task 1.0 in stage 1.0 \(TID 3, localhost, executor driver, partition 1, ANY, 4621 bytes\)

17/08/09 17:51:18 INFO TaskSetManager: Finished task 0.0 in stage 1.0 \(TID 2\) in 90 ms on localhost \(executor driver\) \(1/2\)

17/08/09 17:51:18 INFO Executor: Running task 1.0 in stage 1.0 \(TID 3\)

17/08/09 17:51:18 INFO ShuffleBlockFetcherIterator: Getting 1 non-empty blocks out of 2 blocks

17/08/09 17:51:18 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 0 ms

17/08/09 17:51:18 INFO Executor: Finished task 1.0 in stage 1.0 \(TID 3\). 1285 bytes result sent to driver

17/08/09 17:51:18 INFO TaskSetManager: Finished task 1.0 in stage 1.0 \(TID 3\) in 25 ms on localhost \(executor driver\) \(2/2\)

17/08/09 17:51:18 INFO TaskSchedulerImpl: Removed TaskSet 1.0, whose tasks have all completed, from pool 

17/08/09 17:51:18 INFO DAGScheduler: ResultStage 1 \(collect at WordCount.scala:37\) finished in 0.117 s

17/08/09 17:51:18 INFO DAGScheduler: Job 0 finished: collect at WordCount.scala:37, took 0.964584 s

\(hello,2\)

\(world,1\)

\(hadoop,1\)

17/08/09 17:51:18 INFO SparkUI: Stopped Spark web UI at http://127.0.0.1:4040

17/08/09 17:51:18 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!

17/08/09 17:51:18 INFO MemoryStore: MemoryStore cleared

17/08/09 17:51:18 INFO BlockManager: BlockManager stopped

17/08/09 17:51:18 INFO BlockManagerMaster: BlockManagerMaster stopped

17/08/09 17:51:18 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!

17/08/09 17:51:18 INFO SparkContext: Successfully stopped SparkContext

17/08/09 17:51:18 INFO ShutdownHookManager: Shutdown hook called

17/08/09 17:51:18 INFO ShutdownHookManager: Deleting directory /private/var/folders/r9/35q9g3d56\_d9g0v59w9x2l9w0000gn/T/spark-61f586cf-5f82-44db-bb29-fc8f797e690e



