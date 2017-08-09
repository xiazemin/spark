# 读本地文件

package spark



import org.apache.spark.\_

import SparkContext.\_

object WordCount {

   def main\(args: Array\[String\]\) {

     val conf = new SparkConf\(\)

             .setMaster\("local"\)

             .setAppName\("CountingSheep"\)

             .set\("spark.executor.memory", "1g"\)

     val sc = new SparkContext\(conf\)     

val file=sc.textFile\("file:///Users/didi/java/WordCount/input/\*.txt"\)

     val count=file.flatMap\(line=&gt;line.split\(" "\)\).map\(word=&gt;\(word,1\)\).reduceByKey\(\_+\_\)

    count.collect.foreach\(println\)

     sc.stop\(\)

 }

}

