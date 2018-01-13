# chapter9

spark on hive

ls: cannot access /home/hadoop/spark-2.2.0-bin-hadoop2.6/lib/spark-assembly-\*.jar: No such file or directory

出现这个问题的原因是

spark升级到spark2以后，原有lib目录下的大JAR包被分散成多个小JAR包，原来的spark-assembly-\*.jar已经不存在，所以hive没有办法找到这个JAR包。

解决方法

打开hive的安装目录下的bin目录，找到hive文件

cd $HIVE\_HOME/bin

vi hive

comment the following lines as follows

\# add Spark assembly jar to the classpath

\#if \[\[ -n "$SPARK\_HOME" \]\]

\#then

\#  sparkAssemblyPath=\`ls ${SPARK\_HOME}/lib/spark-assembly-\*.jar\`

\#  CLASSPATH="${CLASSPATH}:${sparkAssemblyPath}"

\#fi

