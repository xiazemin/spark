# mac 单机版 spark 安装

下载spark压缩包并解压

到官网下载spark的安装包（我用的是spark-2.0.1-bin-hadoop2.7.tgz）

[http://mirror.bit.edu.cn/apache/spark/](http://mirror.bit.edu.cn/apache/spark/)

进入安装包存放目录，解压安装包

tar -zxvf  spark-2.0.1-bin-hadoop2.7.tgz

解压后进入conf 文件夹下将 spark-env.sh.template 改名为 spark-env.sh

mv spark-env.sh.template spark-env.sh

修改spark的配置文件

修改 spark-env.sh 文件添加信息

vi spark-env.sh

//添加如下信息

export SCALA\_HOME=/Users/didi/scala/

export JAVA\_HOME=/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/

export SPARK\_MASTER\_IP=127.0.0.1

export SPARK\_WORKER\_MEMORY=512m

export master=spark://127.0.0.1:7070

修改 slaves.template 添加信息

vi slaves.template

//添加如下信息

master

\#localhost

配置环境变量

Mac修改 .bash\_profile 文件，此文件是mac 当前用户的环境配置文件。

/etc/profile 是当前系统的环境配置文件（Linux，系统可修改这个）

.bash\_profile 文件的路径是在当前用户下。

操作如下：

新打开终端输入命令

vi .bash\_profile

//添加如下信息

\#SPARK VARIABLES START

export SPARK\_HOME=/Users/yangyibo/Software/spark-2.0.1-bin-hadoop2.7

export PATH=$PATH:$SPARK\_HOME/bin

1

2

3

4

5

1

2

3

4

5

如下图，mac 注意路径哦，

这里写图片描述

这里写图片描述

验证安装情况

此时就可以检验成果喽

进入安装包的sbin 目录执行 start-all.sh 脚本

./start-all.sh

1

1

这里写图片描述

此时可能会出现这个情况，我们可以直接忽略

这里写图片描述

此时我们可以通过jps 命令 检验运行情况

这里写图片描述

打开Spark Master 页面查看集群情况，我相信你会直接点击。

[http://localhost:8080/](http://localhost:8080/)

如图

这里写图片描述

停止spark

进入spark的sbin目录，执行命令

$ ./stop-all.sh

1

1

接下来就是spark 的小程序了

Spark进阶体验

