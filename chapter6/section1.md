# spark streaming

程序：

package spark

import org.apache.spark.\_

import org.apache.spark.streaming.\_

import org.apache.spark.streaming.StreamingContext.\_ // not necessary since Spark 1.3

object Streaming {

def main\(args:Array\[String\]\)  {

// Create a local StreamingContext with two working thread and batch interval of 1 second.

// The master requires 2 cores to prevent from a starvation scenario.

val conf = new SparkConf\(\).setMaster\("local\[2\]"\).setAppName\("NetworkWordCount"\)

val ssc = new StreamingContext\(conf, Seconds\(1\)\)

// Create a DStream that will connect to hostname:port, like localhost:9999

val lines = ssc.socketTextStream\("localhost", 9999\)

// Split each line into words

val words = lines.flatMap\(\_.split\(" "\)\)

// Count each word in each batch

val pairs = words.map\(word =&gt; \(word, 1\)\)

val wordCounts = pairs.reduceByKey\(\_ + \_\)

// Print the first ten elements of each RDD generated in this DStream to the console

wordCounts.print\(\)

ssc.start\(\)             // Start the computation

ssc.awaitTermination\(\)  // Wait for the computation to terminate

}

}

工程－》导出－》导出jar包streaming.jar

$ ./bin/spark-submit --class spark.Streaming  streaming.jar localhost 9999

$     nc -lk 9999

sasfs  fgd

dfg   gdggh  rry vdfg dff

17/08/11 11:36:52 INFO scheduler.TaskSchedulerImpl: Removed TaskSet 72.0, whose tasks have all completed, from pool

17/08/11 11:36:52 INFO scheduler.DAGScheduler: ResultStage 72 \(print at Streaming.scala:24\) finished in 0.006 s

17/08/11 11:36:52 INFO scheduler.DAGScheduler: Job 36 finished: print at Streaming.scala:24, took 0.012587 s

-------------------------------------------

Time: 1502422612000 ms

-------------------------------------------

\(,3\)

\(dff,1\)

\(vdfg,1\)

\(gdggh,1\)

\(dfg,1\)

\(rry,1\)



