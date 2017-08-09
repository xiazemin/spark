# spark  shell wordcount

$hadoop/hadoop/sbin/start-all.sh

$ jps

44368

46896 ResourceManager

46976 NodeManager

45399 Master

47015 Jps

46792 SecondaryNameNode

46697 DataNode

39754

45435 Worker

637 Main

46622 NameNode

$ hdfs dfs -lsr /

-rw-r--r--   1 didi supergroup         12 2017-08-08 10:16 /input/input1.txt

-rw-r--r--   1 didi supergroup         13 2017-08-08 10:16 /input/input2.txt

$ ./bin/spark-shell

Setting default log level to "WARN".

scala&gt; var file=sc.textFile\("hdfs:/input/input1.txt"\)

file: org.apache.spark.rdd.RDD\[String\] = hdfs:/input/input1.txt MapPartitionsRDD\[1\] at textFile at &lt;console&gt;:24



scala&gt; val count=file.flatMap\(line=&gt;line.split\(" "\)\).map\(word=&gt;\(word,1\)\).reduceByKey\(\_+\_\)

count: org.apache.spark.rdd.RDD\[\(String, Int\)\] = ShuffledRDD\[4\] at reduceByKey at &lt;console&gt;:26



scala&gt; count.collect

res0: Array\[\(String, Int\)\] = Array\(\(hello,1\), \(world,1\)\)



scala&gt;

