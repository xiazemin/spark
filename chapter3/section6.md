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

	at sun.nio.ch.Net.bind0\(Native Method\)

