# section1

式下，默认读的都是hdfs文件系统，这几种模式下很难读取本地文件（这是很显然的事情，但你可以通过指定节点的文件服务曲线救国）。

下面的代码在local模式下有效，在其它模式下无效：

var theP1 = sc.textFile\("file:///usr/test/people.json"\)  //读取本地

var theP2 = sc.textFile\("hdfs://master.Hadoop/user/root/test/test/people.json"\) //读取hdfs文件

下面的代码在非local模式下，都是读取的hdfs,file://模式无效.

var theP1 = sc.textFile\("/usr/test/people.json"\)  

var theP2 = sc.textFile\("/user/root/test/test/people.json"\)

下面这个语句在几种模式下都有效

var theP2 = sc.textFile\("hdfs://master.hadoop/user/root/test/test/people.json"\) //读取hdfs文件

在非local模式下等同于

var theP2 = sc.textFile\("/user/root/test/test/people.json"\)



