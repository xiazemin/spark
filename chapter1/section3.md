# 问题解决

$ ./start-all.sh

starting org.apache.spark.deploy.master.Master, logging to /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.master.Master-1-localhost.out

failed to launch: nice -n 0 /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/bin/spark-class org.apache.spark.deploy.master.Master --host localhost --port 7077 --webui-port 8080

  	at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:301\)

  	at java.lang.ClassLoader.loadClass\(ClassLoader.java:247\)

full log in /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.master.Master-1-localhost.out

Password:

localhost: starting org.apache.spark.deploy.worker.Worker, logging to /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-localhost.out

localhost: failed to launch: nice -n 0 /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/bin/spark-class org.apache.spark.deploy.worker.Worker --webui-port 8081 spark://localhost:7077

localhost:   	at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:301\)

localhost:   	at java.lang.ClassLoader.loadClass\(ClassLoader.java:247\)

localhost: full log in /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.worker.Worker-1-localhost.out



