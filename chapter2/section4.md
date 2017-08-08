# 配置环境

$   vi .bashrc

export SPARK\_HOME=/Users/didi/spark/spark

$ cp conf/spark-env.sh.template conf/spark-env.sh

$ vi conf/spark-env.sh

export JAVA\_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0\_144.jdk/Contents/Home/

export SCALA\_HOME=/Users/didi/scala/

export SPARK\_MASTER\_IP=127.0.0.1

export SPARK\_WORKER\_MEMORY=512m

export master=spark://127.0.0.1:7070

$ vi conf/slaves.template

master

\#localhost

