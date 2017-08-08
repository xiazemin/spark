# Eclipse Scala IDE , run as -&gt; Scala Application不见了

同一个package里不能同时有Java和Scala文件

最后发现是main object最上面的package写错了

还有可能是其他scala文件出现错误，阻止了项目的正常编译。

还有可能是因为pom中引入了错误的或不当的依赖引起的

打开eclipse.新建一个scala Project.第一次新建的话应该在other里面,名称随便,然后新建一个scala Object ,名称叫"HelloWorld".

scala和Java不同,文件的名称不必和Object的名称相同.

package com.scala.demo

object HelloWorld {

def main\(args:Array\[String\]\)

{

```
println\("Hello world!"\)  
```

}

}



