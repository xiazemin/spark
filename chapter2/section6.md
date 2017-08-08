# master 无法启动

$ sbin/start-all.sh

starting org.apache.spark.deploy.master.Master, logging to /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.master.Master-1-bogon.out

failed to launch: nice -n 0 /Users/didi/spark/spark/bin/spark-class org.apache.spark.deploy.master.Master --host bogon --port 7077 --webui-port 8080

```
  at io.netty.util.concurrent.DefaultThreadFactory$DefaultRunnableDecorator.run\(DefaultThreadFactory.java:144\)

  at java.lang.Thread.run\(Thread.java:748\)
```

full log in /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.master.Master-1-bogon.out

Password:

localhost: starting org.apache.spark.deploy.worker.Worker, logging to /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-bogon.out

$ cat /Users/didi/spark/spark/logs/spark-didi-org.apache.spark.deploy.master.Master-1-bogon.out

17/08/08 21:19:29 WARN util.Utils: Service 'sparkMaster' could not bind on port 7092. Attempting port 7093.

Exception in thread "main" java.net.BindException: Can't assign requested address: Service 'sparkMaster' failed after 16 retries \(starting from 7077\)! Consider explicitly settin\#

解决办法：

在spark-env.sh中配置：

export  SPARK\_MASTER\_HOST=127.0.0.1

export  SPARK\_LOCAL\_IP=127.0.0.1

更多如下：

```
export SPARK_WORKER_MEMORY=512m
```

```
export SPARK_EXECUTOR_MEMORY=512m
export SPARK_WORKER_INSTANCES=1
export SPARK_WORKER_CORES=1
export SPARK_WORKER_DIR=/home/ubuntu/spark
export SPARK_LOCAL_IP=52.211.60.97
export SPARK_MASTER_IP=52.211.60.97
export SPARK_MASTER_WEBUI_PORT=4041
```



