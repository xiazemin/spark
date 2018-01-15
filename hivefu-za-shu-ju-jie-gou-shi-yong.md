Hive的数据类型解析和表的操作实例

1：Array

```
 顾名思义就是数组，使用方式 array&lt;&gt;

 1\)：创建表

 拿电影数据为例，数据的维度包括
 创建movie\_message表：
```

create table movie\_message\(

```
id int,  

title string,  

daoyan array&lt;string&gt;,  

bianju array&lt;string&gt;,  

leixing array&lt;string&gt;,  

zhuyan array&lt;string&gt;,  

year int,  

month int,  

shichang int,  

disnum int,  

score float  
```

\)

comment "this table about movie's message"

row format delimited fields terminated by ","

collection items terminated by '/';

```
  加载数据（可以从本地加载，也可以从hdfs装载，当然也可以从别的表中查询结果进行转载），这里从本地装载
```

load data local inpath "/home/master/mycode/new\_movies\_load.csv" into table movie\_message;

```
2\)：查看array的元素

  用下标进行寻找，类似于其他编程语言中的数组访问
```

hive&gt; select leixing\[0\] from movie\_message limit 5;

OK

剧情

剧情

剧情

纪录片

喜剧

Time taken: 1.116 seconds, Fetched: 5 row\(s\)

3\)：内嵌查询及统计

```
 这里就是 写在前边的话中提到的问题，这里使用explode和lateral view关键字，应该这样写
```

select lx,count\(\*\) from movie\_message lateral view explode\(leixing\) leixing as lx group by lx;

```
   结果为：
```

传记    194

儿童    18

冒险    242

剧情    1490

动作    252

动画    106

历史    208

古装    9

同性    84

喜剧    618

奇幻    178

家庭    130

恐怖    152

悬念    2

悬疑    386

情色    19

惊悚    435

戏曲    11

战争    144

歌舞    40

武侠    1

灾难    11

爱情    404

犯罪    442

真人秀  6

短片    165

科幻    165

纪录片  620

脱口秀  10

舞台艺术        8

西部    6

运动    29

音乐    123

鬼怪    1

黑色电影        4

```
 4\)：如何保存查询结果

   这里使用overwrite方法，只需在你的语句前加上即可
```

insert overwrite local directory "you path"

```
      也可以指定字段之间的分隔符
```

row format delimited fields terminated by "\t"

```
      还是上边统计类型的例子，这里将其查询结果保存在本地/home/master/mycode/movie\_leixing
```

insert overwrite local directory "/home/master/mycode/movie\_leixing"

row format delimited fields terminated by "\t"

select lx,count\(\*\) from movie\_message lateral view explode\(leixing\) leixing as lx group by lx;

2：Map

```
 就是&lt;key:value&gt;这样的键值对，假设我们有这样格式的数据人物A，主演了BCD电影，将于2016-05上映
```

A       ABC:2016-05,EFG:2016-09

B       OPQ:2015-06,XYZ:2016-04

```
 1\)：创建表
```

create table people\_movie\(

name string,

movie map&lt;string,string&gt; \)

row format delimited fields terminated by "\t"

collection items terminated by ","

map keys terminated by ":";

load data local inpath "/home/master/map" into table people\_movie;

```
 2\)：普通查看表数据
```

hive&gt; select \* from people\_movie;

OK

A       {"ABC":"2016-05","EFG":"2016-09"}

B       {"OPQ":"2015-06","XYZ":"2016-04"}

A       {"ABC":"2016-05","EFG":"2016-09"}

B       {"OPQ":"2015-06","XYZ":"2016-04"}

Time taken: 0.148 seconds, Fetched: 4 row\(s\)

hive&gt; select movie\['ABC'\] from people\_movie;

OK

2016-05

NULL

2016-05

NULL

Time taken: 0.144 seconds, Fetched: 4 row\(s\)

```
3\)：使用explode关键字查询
```

hive&gt; select explode\(movie\) as \(m\_name,m\_time\) from people\_movie;

OK

ABC     2016-05

EFG     2016-09

OPQ     2015-06

XYZ     2016-04

ABC     2016-05

EFG     2016-09

OPQ     2015-06

XYZ     2016-04

Time taken: 0.121 seconds, Fetched: 8 row\(s\)

4\)：使用explode和lateral view结合查询

\[html\] view plain copy

hive&gt; select name,mo,time from people\_movie lateral view explode\(movie\) movie as mo,time;

OK

A       ABC     2016-05

A       EFG     2016-09

B       OPQ     2015-06

B       XYZ     2016-04

A       ABC     2016-05

A       EFG     2016-09

B       OPQ     2015-06

B       XYZ     2016-04

Time taken: 0.147 seconds, Fetched: 8 row\(s\)

3：Structs

```
 类似于C语言中的结构体，内部数据通过X.X来获取，假设我们的数据格式是这样的，电影ABC，有1254人评价过，打分为7.4分
```

\[html\] view plain copy

ABC     1254:7.4

DEF     256:4.9

XYZ     456:5.4

```
 1\)：创建数据表
```

\[html\] view plain copy

Time taken: 0.147 seconds, Fetched: 8 row\(s\)

hive&gt; create table movie\_score\(

```
&gt; name string,  

&gt; info struct&lt;number:int,score:float&gt;  

&gt; \)row format delimited fields terminated by "\t"  

&gt; collection items terminated by ":";  

 2\)：查询表数据
```

\[html\] view plain copy

hive&gt; select \* from movie\_score;

OK

ABC     {"number":1254,"score":7.4}

DEF     {"number":256,"score":4.9}

XYZ     {"number":456,"score":5.4}

Time taken: 0.103 seconds, Fetched: 3 row\(s\)

hive&gt; select info.number,info.score from movie\_score;

OK

1254    7.4

256     4.9

456     5.4

Time taken: 0.148 seconds, Fetched: 3 row\(s\)

4：collect\_set函数

```
 这里再另外介绍一个函数collect\_set\(\)，该函数的作用是将某字段的值进行去重汇总，产生Array类型字段，假设数据格式如下：
```

\[html\] view plain copy

hive&gt; select \* from test;

OK

1       A

1       C

1       B

2       B

2       C

2       D

3       B

3       C

3       D

Time taken: 0.096 seconds, Fetched: 6 row\(s\)

```
  现在要统计每个id得到的等级
```

\[html\] view plain copy

select id,collect\_set\(name\) from test group by id;

```
  结果为
```

\[html\] view plain copy

Total MapReduce CPU Time Spent: 3 seconds 360 msec

OK

1       \["A","C","B"\]

2       \["B","C","D"\]

3       \["B","C","D"\]

Time taken: 32.298 seconds, Fetched: 3 row\(s\)

