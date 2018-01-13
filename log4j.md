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



完整日志配置

log4j.rootLogger=CONSOLE,FILE  

log4j.addivity.org.apache=true  

  

\# 应用于控制台  

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender  

log4j.appender.CONSOLE.Threshold=INFO  

log4j.appender.CONSOLE.Target=System.out  

log4j.appender.CONSOLE.Encoding=GBK  

log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout  

log4j.appender.CONSOLE.layout.ConversionPattern=\[framework\] %d - %c -%-4r \[%t\] %-5p %c %x - %m%n  

  

\# 每天新建日志  

log4j.appender.A1=org.apache.log4j.DailyRollingFileAppender  

log4j.appender.A1.File=C:/log4j/log  

log4j.appender.A1.Encoding=GBK  

log4j.appender.A1.Threshold=DEBUG  

log4j.appender.A1.DatePattern='.'yyyy-MM-dd  

log4j.appender.A1.layout=org.apache.log4j.PatternLayout  

log4j.appender.A1.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L : %m%n  

  

\#应用于文件  

log4j.appender.FILE=org.apache.log4j.FileAppender  

log4j.appender.FILE.File=C:/log4j/file.log  

log4j.appender.FILE.Append=false  

log4j.appender.FILE.Encoding=GBK  

log4j.appender.FILE.layout=org.apache.log4j.PatternLayout  

log4j.appender.FILE.layout.ConversionPattern=\[framework\] %d - %c -%-4r \[%t\] %-5p %c %x - %m%n  

  

\# 应用于文件回滚  

log4j.appender.ROLLING\_FILE=org.apache.log4j.RollingFileAppender  

log4j.appender.ROLLING\_FILE.Threshold=ERROR  

log4j.appender.ROLLING\_FILE.File=rolling.log  

log4j.appender.ROLLING\_FILE.Append=true  

log4j.appender.CONSOLE\_FILE.Encoding=GBK  

log4j.appender.ROLLING\_FILE.MaxFileSize=10KB  

log4j.appender.ROLLING\_FILE.MaxBackupIndex=1  

log4j.appender.ROLLING\_FILE.layout=org.apache.log4j.PatternLayout  

log4j.appender.ROLLING\_FILE.layout.ConversionPattern=\[framework\] %d - %c -%-4r \[%t\] %-5p %c %x - %m%n  

  

\#自定义Appender  

log4j.appender.im = net.cybercorlin.util.logger.appender.IMAppender  

log4j.appender.im.host = mail.cybercorlin.net  

log4j.appender.im.username = username  

log4j.appender.im.password = password  

log4j.appender.im.recipient = yyflyons@163.com  

log4j.appender.im.layout=org.apache.log4j.PatternLayout  

log4j.appender.im.layout.ConversionPattern =\[framework\] %d - %c -%-4r \[%t\] %-5p %c %x - %m%n  

  

\#应用于socket  

log4j.appender.SOCKET=org.apache.log4j.RollingFileAppender  

log4j.appender.SOCKET.RemoteHost=localhost  

log4j.appender.SOCKET.Port=5001  

log4j.appender.SOCKET.LocationInfo=true  

\# Set up for Log Facter 5  

log4j.appender.SOCKET.layout=org.apache.log4j.PatternLayout  

log4j.appender.SOCET.layout.ConversionPattern=\[start\]%d{DATE}\[DATE\]%n%p\[PRIORITY\]%n%x\[NDC\]%n%t\[THREAD\]%n%c\[CATEGORY\]%n%m\[MESSAGE\]%n%n  

\# Log Factor 5 Appender  

log4j.appender.LF5\_APPENDER=org.apache.log4j.lf5.LF5Appender  

log4j.appender.LF5\_APPENDER.MaxNumberOfRecords=2000  

  

\# 发送日志给邮件  

log4j.appender.MAIL=org.apache.log4j.net.SMTPAppender  

log4j.appender.MAIL.Threshold=FATAL  

log4j.appender.MAIL.BufferSize=10  

log4j.appender.MAIL.From=yyflyons@163.com  

log4j.appender.MAIL.SMTPHost=www.wusetu.com  

log4j.appender.MAIL.Subject=Log4J Message  

log4j.appender.MAIL.To=yyflyons@126.com  

log4j.appender.MAIL.layout=org.apache.log4j.PatternLayout  

log4j.appender.MAIL.layout.ConversionPattern=\[framework\] %d - %c -%-4r \[%t\] %-5p %c %x - %m%n 



