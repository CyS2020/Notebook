## log for java

### 1. 能干什 用什么样式-用哪个控制器-输出到哪里

-   日志监控打印，在项目运行期间需要记录用户所有操作
-   添加新的内容，比如时间和线程
-   程序调试期间，记录运行的步骤和运行行
-   成功上下稳定后，不需要打印
-   多个日志输出源

## 2. 四个关键 properties

-   目的地 [appender]：默认两个输出源，控制台，存到本地硬盘

    //输出到本地文件

    log4j.appender.xxx.File = org.apache.log4j.DailyRollingFileAppender

    log4j.appender.xxx.File.file = d:\ file path

    log4j.appender.xxx.File.DatePattern = .yyyy-MM-dd
    
    //输出到控制台 xxx代指公司名
    
    log4j.appender.xxx.Console= org.apache.log4j.ConsoleAppender
    
- 布局 [layout]: 输出的样式

  log4j.appender.xxx.File.layout = org.apache.log4j.PatternLayout
  
  log4j.appender.xxx.File.layout.ConversionPattern = 
  
  ​                                                                                    %d{yyyy-MM-dd HH:mm:ss, SSS} %5p (%C:%M) - %m%n
  
  
  
  log4j.appender.xxx.Console.layout = org.apache.log4j.PatternLayout
  
  log4j.appender.xxx.Console.layout.ConversionPattern = 
  
  ​                                                                                    %d{yyyy-MM-dd HH:mm:ss, SSS} %5p (%C:%M) - %m%n
  
- 控制单元 [logger]: 控制输出级别，控制输出源

    log4j.rootLogger = debug,xxx.file,xxx.Console

    log4j.logger.com.xxx = error, xxx.Console, xxx.file

-   级别 [level]：debug < info < warn < error  

    打印自身往后靠

    级别取精确输出为各自

    xml在properties前

### 3. 工作使用

-   工作中使用xml进行配置

    filter进行紧缺匹配，过滤我想要的

    additivity = false 精确匹配，停止传播

-   在catch代码中写:

    e.getStackTrace() 		//一般注释掉

    logger.error(e.getMessage(), e.getCause());

    throw xxxException 	//抛出自定义异常

