在使用Log4j时若提示如下信息：

log4j:WARN No appenders could be found for logger \(org.apache.ibatis.logging.LogFactory\).

log4j:WARN Please initialize the log4j system properly.

则，解决办法为：在**项目的src**下面新建file名为log4j.properties文件，内容如下:

\# Configure logging for testing: optionally with log file

\#可以设置级别：debug&gt;info&gt;error

\#debug:可以显式debug,info,error

\#info:可以显式info,error

\#error:可以显式error

log4j.rootLogger=debug,appender1

\#log4j.rootLogger=info,appender1

\#log4j.rootLogger=error,appender1

\#输出到控制台

log4j.appender.appender1=org.apache.log4j.ConsoleAppender

\#样式为TTCCLayout

log4j.appender.appender1.layout=org.apache.log4j.TTCCLayout

然后，存盘退出。再次运行程序就会显示Log信息了。

通过配置文件可知，我们需要配置3个方面的内容：

1、根目录（级别和目的地）；

2、目的地（控制台、文件等等）；

3、输出样式。

或者，使用下面的内容也可以：

\# Configure logging for testing: optionally with log file

log4j.rootLogger=WARN, stdout

\# log4j.rootLogger=WARN, stdout, logfile

log4j.appender.stdout=org.apache.log4j.ConsoleAppender

log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

log4j.appender.stdout.layout.ConversionPattern=%d %p \[%c\] - %m%n

log4j.appender.logfile=org.apache.log4j.FileAppender

log4j.appender.logfile.File=target/spring.log

log4j.appender.logfile.layout=org.apache.log4j.PatternLayout

log4j.appender.logfile.layout.ConversionPattern=%d %p \[%c\] - %m%n

