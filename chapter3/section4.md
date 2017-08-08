# eclipse+spark

build path-&gt;config build path  加入（spark/jars）的jar包，全选，应用



package sparkscala01



import org.apache.spark.\_

import SparkContext.\_

object WordCount {

def main\(args:Array\[String\]\):Unit= {

  

  val conf =new SparkConf\(\).setMaster\("local\[4\]"\).setAppName\("word count"\)

  val sc = new SparkContext\(conf\)

  val textFile =sc.textFile\("/Users/wxzyhx/Downloads/paper1.txt"\)

//val result = textFile.flatMap\(line =&gt; line.split\("\\s+"\)\).map\(word =&gt; \(word, 1\)\).reduceByKey\(\_ + \_\)

  textFile.flatMap\(\_.split\(" "\)\).map\(\(\_,1\)\).reduceByKey\(\_+\_\).collect\(\).foreach\(println\)



  }

}

