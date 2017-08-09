# section2

./bin/spark-shell

scala&gt; val file=sc.textFile\("hdfs:/"\)

scala&gt; file.collect.foreach\(println\)

java.io.IOException: Not a file: hdfs://localhost:8020/input



