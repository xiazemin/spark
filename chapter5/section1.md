# hdfs文件路径

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

  val file=sc.textFile\("hdfs://localhost:8020/input/\*.txt"\)

 val count=file.flatMap\(line=&gt;line.split\(" "\)\).map\(word=&gt;\(word,1\)\).reduceByKey\(\_+\_\)

count.collect.foreach\(println\)

 sc.stop\(\)
```

}

}

17/08/09 20:48:55 INFO Executor: Running task 0.0 in stage 1.0 \(TID 2\)

17/08/09 20:48:55 INFO ShuffleBlockFetcherIterator: Getting 2 non-empty blocks out of 2 blocks

17/08/09 20:48:55 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 6 ms

17/08/09 20:48:55 INFO Executor: Finished task 0.0 in stage 1.0 \(TID 2\). 1265 bytes result sent to driver

17/08/09 20:48:55 INFO TaskSetManager: Starting task 1.0 in stage 1.0 \(TID 3, localhost, executor driver, partition 1, ANY, 4621 bytes\)

17/08/09 20:48:55 INFO Executor: Running task 1.0 in stage 1.0 \(TID 3\)

17/08/09 20:48:55 INFO TaskSetManager: Finished task 0.0 in stage 1.0 \(TID 2\) in 72 ms on localhost \(executor driver\) \(1/2\)

17/08/09 20:48:55 INFO ShuffleBlockFetcherIterator: Getting 1 non-empty blocks out of 2 blocks

17/08/09 20:48:55 INFO ShuffleBlockFetcherIterator: Started 0 remote fetches in 0 ms

17/08/09 20:48:55 INFO Executor: Finished task 1.0 in stage 1.0 \(TID 3\). 1285 bytes result sent to driver

17/08/09 20:48:55 INFO TaskSetManager: Finished task 1.0 in stage 1.0 \(TID 3\) in 20 ms on localhost \(executor driver\) \(2/2\)

17/08/09 20:48:55 INFO TaskSchedulerImpl: Removed TaskSet 1.0, whose tasks have all completed, from pool 

17/08/09 20:48:55 INFO DAGScheduler: ResultStage 1 \(collect at WordCount.scala:39\) finished in 0.091 s

17/08/09 20:48:55 INFO DAGScheduler: Job 0 finished: collect at WordCount.scala:39, took 0.881534 s

\(hello,2\)

\(world,1\)

\(hadoop,1\)

17/08/09 20:48:55 INFO SparkUI: Stopped Spark web UI at http://172.24.20.42:4042

17/08/09 20:48:55 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!

17/08/09 20:48:55 INFO MemoryStore: MemoryStore cleared

17/08/09 20:48:55 INFO BlockManager: BlockManager stopped

17/08/09 20:48:55 INFO BlockManagerMaster: BlockManagerMaster stopped

17/08/09 20:48:55 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!

17/08/09 20:48:55 INFO SparkContext: Successfully stopped SparkContext

17/08/09 20:48:55 INFO ShutdownHookManager: Shutdown hook called

