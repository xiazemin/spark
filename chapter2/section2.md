# 端口



hadoop



如果ssh端口不是默认的22，在etc/Hadoop/hadoop-env.sh里改下。如：



export HADOOP\_SSH\_OPTS="-p 18921"



spark

在Spark-env.sh添加

export SPARK\_SSH\_OPTS="-p 37294"



ps :spark master 默认web 端口是8080，如果8080被占用，spark会自动就加1.所以当8080你访问不到spark时，你可以试试8081.worker是8082

