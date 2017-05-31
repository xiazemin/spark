# mac java 升级

$ /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/bin/java -version

java version "1.8.0\_131"

Java\(TM\) SE Runtime Environment \(build 1.8.0\_131-b11\)

Java HotSpot\(TM\) 64-Bit Server VM \(build 25.131-b11, mixed mode\)

$ vi .bashrc

\#export JAVA\_HOME=/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/

export JAVA\_HOME=/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/

$ java -version

java version "1.8.0\_131"

Java\(TM\) SE Runtime Environment \(build 1.8.0\_131-b11\)

Java HotSpot\(TM\) 64-Bit Server VM \(build 25.131-b11, mixed mode\)

$    vi ../conf/spark-env.sh

\#export JAVA\_HOME=/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/

export JAVA\_HOME=/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/

