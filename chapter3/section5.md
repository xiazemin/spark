# Eclipse Scala IDE , run as -&gt; Scala Application不见了

同一个package里不能同时有Java和Scala文件

最后发现是main object最上面的package写错了

还有可能是其他scala文件出现错误，阻止了项目的正常编译。



还有可能是因为pom中引入了错误的或不当的依赖引起的

