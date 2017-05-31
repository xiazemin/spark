# 问题解决

$ ./start-all.sh

starting org.apache.spark.deploy.master.Master, logging to /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.master.Master-1-localhost.out

failed to launch: nice -n 0 /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/bin/spark-class org.apache.spark.deploy.master.Master --host localhost --port 7077 --webui-port 8080

```
  at sun.misc.Launcher$AppClassLoader.loadClass\(Launcher.java:301\)

  at java.lang.ClassLoader.loadClass\(ClassLoader.java:247\)
```

full log in /Users/didi/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-didi-org.apache.spark.deploy.master.Master-1-localhost.out

localhost: ssh: connect to host localhost port 22: Connection refused

localhost:sbin didi$  ssh localhost

ssh: connect to host localhost port 22: Connection refused

$  sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist



WARNING: Improper use of the sudo command could lead to data loss

or the deletion of important system files. Please double-check your

typing when using sudo. Type "man sudo" for more information.



To proceed, enter your password, or type Ctrl-C to abort.



Password:

