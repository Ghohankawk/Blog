---
title: 关于log4j的使用说明
---

 
# 今天要分享的内容是log4j的使用。
每个系统都有日志记录，而多数都是用的log4j，以为会配置了，懂了，发现还是有些细节的问题，要问，说不上来
<!-- more --> 

# 先来直接看配置吧和测试用例吧
jar包的依赖
```angular2html
    <!-- log4j support -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
```
log4j.properties文件如下，放在你的web的resource下面，推荐maven标准工程来
```angular2html
### 设置###
log4j.rootLogger = DEBUG,stdout,D,E

log4j.logger.A=DEBUG, stdout
log4j.logger.A.B=INFO, stdout  

### 输出信息到控制抬 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

### 输出DEBUG 级别以上的日志到=D://logs/error.log ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = D://logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

### 输出ERROR 级别以上的日志到=D://logs/error.log ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =D://logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

```
测试用例
```angular2html

import org.apache.log4j.Logger;

/**
 * @author hankun
 * @create 2017-06-06 18:32
 */
public class log4jTtest {
    private static Logger logger = Logger.getLogger(log4jTtest.class);
    private static Logger loggerA = Logger.getLogger("A");
    private static Logger loggerA_B = Logger.getLogger("A.B");

    /**
     * @param args
     */
    public static void main(String[] args) {

        // 记录debug级别的信息
        logger.debug("This is debug message.");
        // 记录info级别的信息
        logger.info("This is info message.");
        // 记录error级别的信息
        logger.error("This is error message.");

        loggerA_B.info("repeat three times in console");
        loggerA.info("repeat two times in console");
        logger.info("repeat one times in console");
    }

}

```


# 注意上面的输出结果，你知道输出啥内容，则，就真正理解了

```angular2html
[DEBUG] 2017-06-06 19:05:03,477 method:log4jTtest.main(log4jTtest.java:18)
This is debug message.
[INFO ] 2017-06-06 19:05:03,479 method:log4jTtest.main(log4jTtest.java:20)
This is info message.
[ERROR] 2017-06-06 19:05:03,479 method:log4jTtest.main(log4jTtest.java:22)
This is error message.
[INFO ] 2017-06-06 19:05:03,479 method:log4jTtest.main(log4jTtest.java:24)
repeat three times in console
[INFO ] 2017-06-06 19:05:03,479 method:log4jTtest.main(log4jTtest.java:24)
repeat three times in console
[INFO ] 2017-06-06 19:05:03,479 method:log4jTtest.main(log4jTtest.java:24)
repeat three times in console
[INFO ] 2017-06-06 19:05:03,480 method:log4jTtest.main(log4jTtest.java:25)
repeat two times in console
[INFO ] 2017-06-06 19:05:03,480 method:log4jTtest.main(log4jTtest.java:25)
repeat two times in console
[INFO ] 2017-06-06 19:05:03,480 method:log4jTtest.main(log4jTtest.java:26)
repeat one times in console

```

# 问题如下
1. 前面的三个输出，不用解释，都明白为啥
2. 为啥那个loggerA_B，输出了三次，而loggerA输出了二次，logger输出了一次？

# log4j几个重要的概念

- logger组件
  1. 简单的说，它就是决定了应用程序中，哪些代码哪些地方，有可能输出日志
  2. 这也就是为什么，每个类里面上来，就是，logger的初始化，它决定了这个类有资格输出日志
  3. 首先Log4J中总是存在一个rootLogger，即使没有显示配置也是存在的，并且默认输出级别为DEBUG。
  4. Log4J中的层次是用'.'来分隔的，如log4j.logger.com.example.test，这里并不是说log4j.logger后面一定是具体的包名乃至类名，
- appender组件
  1. 输出地，也就是你的日志想输出到哪里，把上面的logger里面的日志
  2. 再简单点，就是输出到控制台和文件里面，也就是上面的配置stdout和D和E
  3. 这个需要和下面的这个概念配合
- 输出控制的概念
  1. Log4j默认把日志信息分为五个等级  all<debug < info < warn < error < fatal<off
  2. 只有大于设置的级别，才会打印日志，低于则忽略
  3. log4j.appender.threshold=ERROR，这种配置用于控制，一个appender的输出级别
- 继承的概念
  1. log4j.rootLogger=DEBUG, Console
  2. log4j.logger.A=DEBUG, Console
  3. log4j.logger.A.B=INFO, Console
  4. A.B继承A，再继承最顶级的root
  5. Log4j是根据Log的名字来判断继承关系的
 
# 来解释一下，上面的输出结果
1. 首先是，debug，info和error前三个日志输出，它们的logger是log4jTtest.class初始化的，继承根logger，因此就是debug级别，同时因为还有D和E的两个appender，所以你在d盘可以看到，有一个info的文件还有一个error的文件，这个日志输出没问题
2. 然后就是解释那重复的三个，因为loggerA_B，它是根据A.B这个参数初始化的，是有继承的，满足A.B的info级别的日志输出，必然满足A的logger的日志输出，当然还有默认的根输出，因此三次
3. 而那个两次的，它的logger是loggerA，继承于根，因此，info的时候，它本身输出一次，根也输出一次，就是两次了。
4. 最后一个，用的是默认的根，当然，只能输出一次了

# 问题来了，怎么避免重复，因为继承父的logger？
    log4j.additivity.A.B=false
    logger A.B的日志仅会输出到自己Console中，不会继承任何父logger的appender。

 就加上面的这句，就解决了问题
 
# 总结

之前反反复复看了很多次，关于日志log4j的配置，每次看完以为会了，但是到了项目里面，总是不知道，自己有时候打印的日志到底跑哪里去了，或者去哪里看？
总结其原因就是，对于细节没有掌握理解到位，比如就是这个logger的概念，如果你真正的明白了，就知道了

谁能产生日志？logger

日志放哪里？appender

什么级别的日志才会输出？日志级别

他们是相互独立的组件，单独配置，又是相互配合，一起使用的，n*m的这种概念

因此上面的配置文件，一个logger可以对应多个appender，而appender之间又是彼此独立的。
