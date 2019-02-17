---
tags: [Spring Boot]
---

## <center>Spring Boot 日志配置</center>

#### 1.日志级别

ALL 最低等级，用于打开所有日志记录。

TRACE 很低的日志级别，一般不会使用。

DEBUG 指出细粒度信息事件对调试应用程序是非常有帮助的，主要用于开发过程中打印一些运行信息。

INFO 消息在粗粒度级别上突出强调应用程序的运行过程。打印一些你感兴趣的或者重要的信息，这个可以用于生产环境中输出程序运行的一些重要信息，但是**不能滥用，避免打印过多的日志**。

WARN 表明会出现潜在错误的情形，有些信息不是错误信息，但是也要给程序员的一些提示。

ERROR 指出虽然发生错误事件，但仍然不影响系统的继续运行。打印错误和异常信息，**如果不想输出太多的日志，可以使用这个级别**。

FATAL 指出每个严重的错误事件将会导致应用程序的退出。这个级别比较高了。重大错误，这种级别你可以直接停止程序了。

OFF 最高等级，用于关闭所有日志记录。

#### 2.在Spring Boot 中配置日志

Spring Boot默认日志是Logback。Logback是log4j框架的作者开发的新一代日志框架，它效率更高、能够适应诸多的运行环境，同时天然支持SLF4J。

1.设置日志文件配置所在的目录

>在application.properties中添加`logging.config=classpath:logback-spring.xml`
>
>在src/main/resources中创建logback-spring.xml

2.logback-spring.xml中配置日志

>```xml
><?xml version="1.0" encoding="UTF-8" ?>
><configuration> 
>	<!-- 设置上下文名称,后面可以引用 -->	
>	<contextName>logback</contextName>		
>	
>	<!-- 控制台输出配置 -->	
>	<appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
>		<!-- 控制日志输出级别 -->	
>		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
>        	<level>INFO</level>
>    	</filter>
>    	<!-- 对日志格式进行编码 -->	
>    	<encoder>
>        	<pattern>%d{HH:mm:ss.SSS} %-5level - %msg%n</pattern>
>    	</encoder>		
>	</appender>
>	      
>	<!-- 文件日志输出配置 -->	
>	<appender name="fileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
>		<!-- 以追加记录的方式写日志，不会清空之前文件中的日志内容 -->
>		<append>true</append>	
>		<!-- 控制日志输出级别 -->	
>		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
>        	<level>DEBUG</level>
>    	</filter>	
>		<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
>          	<!-- 指定日志文件存放到./log文件夹下 -->
>            <fileNamePattern>log/logback.%d{yyyy-MM-dd}.log</fileNamePattern>
>        </rollingPolicy>
>        <encoder>
>            <pattern>%d{HH:mm:ss.SSS} %-5level - %msg%n</pattern>
>        </encoder>
>	</appender>	
>	<!-- 把appender添加到这个logger -->
>	<root>
>		<appender-ref ref="consoleLog" />
>		<appender-ref ref="fileLog"/>
>	</root>
></configuration>
>```
>

