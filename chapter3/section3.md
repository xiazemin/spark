# section3

Spark 自带了很多的example，但是在终端输入run-example，并不会返回其参数，只有简单的一句话，如下：

\[plain\] view plain copy

$ ./bin/run-example   

Usage: run-example &lt;example-class&gt; \[&lt;args&gt;\]  



所以还得自己动手去找，于是在eclipse中导入源码，在源码中可以看到。example有scala和java两个src，下面我们只看scala下面的package.如图



共有7个package

下面把每个package的详细图。





好啦，这就可以看到所有的类名啦。随便找几个运行看看。

examples包下随便找几个看看

\[plain\] view plain copy

./bin/run-example org.apache.spark.examples.SparkPi  

Usage: SparkPi &lt;master&gt; \[&lt;slices&gt;\]  



\[plain\] view plain copy

$ ./bin/run-example org.apache.spark.examples.LocalALS  

Usage: LocalALS &lt;M&gt; &lt;U&gt; &lt;F&gt; &lt;iters&gt;  



\[plain\] view plain copy

$ ./bin/run-example org.apache.spark.examples.SparkTC  

Usage: SparkTC &lt;master&gt; \[&lt;slices&gt;\]  



从上可以看出，只看Usage提示是很晦涩难懂的，需要理解其意义，还得看源码呀。另外我发现有些example运行不了，这个可能是Spark的BUG，正在确认中。



