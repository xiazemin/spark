# 读hadoop功能

val searches = sc.textFile\("hdfs://host:port\_no/data/searches"\)

Default

host: master

port\_no: 9000

完整程序

p

ackage spark



import org.apache.spark.\_



import SparkContext.\_



object WordCount {



def main\(args: Array\[String\]\) {



 val conf = new SparkConf\\(\\)



         .setMaster\\("local"\\)



         .setAppName\\("CountingSheep"\\)



         .set\\("spark.executor.memory", "1g"\\)



 val sc = new SparkContext\\(conf\\)

val file=sc.textFile\("file:///Users/didi/java/WordCount/input/\*.txt"\)



 val count=file.flatMap\\(line=&gt;line.split\\(" "\\)\\).map\\(word=&gt;\\(word,1\\)\\).reduceByKey\\(\\_+\\_\\)



count.collect.foreach\\(println\\)



 sc.stop\\(\\)

}



}

