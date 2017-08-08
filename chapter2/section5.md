# 运行

$ sbin/start-all.sh

starting org.apache.spark.deploy.master.Master, logging to /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.master.Master-1-bogon.out

Password:

localhost: starting org.apache.spark.deploy.worker.Worker, logging to /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-bogon.out

localhost: failed to launch: nice -n 0 /Users/didi/spark/spark/bin/spark-class org.apache.spark.deploy.worker.Worker --webui-port 8081 spark://bogon:7077

localhost:   	at io.netty.util.concurrent.DefaultThreadFactory$DefaultRunnableDecorator.run\(DefaultThreadFactory.java:144\)

localhost:   	at java.lang.Thread.run\(Thread.java:748\)

localhost: full log in /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-bogon.out

$ cat /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-bogon.out

7/08/08 21:14:53 WARN util.Utils: Service 'sparkWorker' could not bind on a random free port. You may check whether configuring an appropriate binding address.

Exception in thread "main" java.net.BindException: Can't assign requested address: Service 'sparkWorker' failed after 16 retries \(on a random free port\)! Consider explicitly setting the appropriate binding address for the service 'sparkWorker' \(for example spark.driver.bindAddress for SparkDriver\) to the correct binding address.

	at sun.nio.ch.Net.bind0\(Native Method\)



