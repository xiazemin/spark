# 运行shell命令



scala&gt; import scala.math.random

import scala.math.random

scala&gt; import org.apache.spark.\_

import org.apache.spark.\_

scala&gt; object SparkPi{

     \| def main\(args:Array\[String\]\){

     \|  val slices=2

     \| val n=100000\*slices

     \| val count=sc.parallelize\(1 to n ,slices\).map{i=&gt;

     \| val x=random\*2 -1

     \| val y=random\*2-1

     \| if\(x\*x +y\*y&lt;1\) 1 else 0

     \| }.reduce\(\_+\_\)

     \|  println\("Pi is roughly "+4.0\*count/n\)

     \| }

     \| }

defined object SparkPi





