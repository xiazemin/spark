# section2

./bin/spark-shell

scala&gt; val file=sc.textFile\("hdfs:/"\)

scala&gt; file.collect.foreach\(println\)

java.io.IOException: Not a file: hdfs://localhost:8020/input

$ vi hadoop/etc//hadoop/core-site.xml

&lt;property&gt;

```
&lt;name&gt;fs.default.name&lt;/name&gt;



&lt;value&gt;hdfs://localhost:8020&lt;/value&gt;
```

&lt;/property&gt;

