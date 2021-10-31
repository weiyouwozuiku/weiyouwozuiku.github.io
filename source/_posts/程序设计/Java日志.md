---
title: Java日志
author: 不二
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/log.jpg
mathjax: true
date: 2021-06-10 16:24:16
tags: 
- Java
- 日志
categories: 程序设计
---

## 日志

- slf4j是日志的规范/接口
- 日志实现工具：log4j/logback/common-logging

## logback

logback是log4j创始人开发的新一款日志组件。SpringBoot默认采用logback作为日志组件。logback框架会默认加载classpath下命名为`logback-spring`或`logback`的配置文件。

日志加载顺序:logback.xml->application.properties->logback-spring.xml

logback主要由以下三个模块组成：

1. logback-core，是整个logback的核心模块
2. logback-classic，支持了slf4j facade
3. logback-access，集成了servlet容器来提供HTTP日志功能，适用于web应用

将所有日志都存储在一个文件中文件大小也随着应用的运行越来越大并且不好排查问题，正确的做法应该是将error日志和其他日志分开，并且不同级别的日志根据时间段进行记录存储。

### 引入方式

- 通过配置xml文件直接引入

  ```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.1.11.RELEASE</version>
      <scope>compile</scope>
    </dependency>
  ```

- 引入spring-boot-starter，会自动引入spring-boot-starter-logging

- 引入spring-boot-starter-web,会自动引入spring-boot-starter

### 使用方式

因为`logback-classic`实现了SLF4J FACADE，所以上层应用只需要面向SLF4J的调用语法即可。使用语法如下：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import ch.qos.logback.classic.LoggerContext;
import ch.qos.logback.core.util.StatusPrinter;

public class HelloWorld2 {

  public static void main(String[] args) {
    //这里的Logger和LoggerFactory均为SLF4J的类，真正调用时会使用Logback的日志能力
    //getLogger方法中传入的是Logger的名称，这个名称在后面讲解配置文件中的<logger>时会继续提到
    Logger logger = LoggerFactory.getLogger("chapters.introduction.HelloWorld2");
    
    //打印一条Debug级别的日志
    logger.debug("Hello world.");

    //获取根Logger，使用场景比较少
    Logger rootLogger = LoggerFactory.getLogger(org.slf4j.Logger.ROOT_LOGGER_NAME);
  }
}
```

### 日志级别

Logback中每一个Logger都有对应的日志级别，该日志级别可以是Logger自己定义的，也可以是从父Logger上继承下来的。Logback一共支持5个日志级别，从高到低分别是ERROR，WARN，INFO，DEBUG，TRACE。Logger的日志级别决定了哪些级别的日志可以被输出。只有大于等于该Logger级别的日志才会被打印出来。比如假设上文中获取的名为"chapters.introduction.HelloWorld2"的Logger日志级别为INFO，则调用logger.debug("xxx")不会输出日志内容，因为DEBUG日志级别低于INFO日志级别。

日志级别可以帮助我们控制日志打印的粒度，比如在开发环境可以将日志级别设置到DEBUG帮助排查问题，而在生产环境则可以将日志级别设置到INFO，从而减少不必要的打印日志带来的性能影响。

### 参数化输出

有时候我们往往并不只是打印出一条完整的日志，而是希望在日志中附带一些运行中参数。这就会带来字符串拼接的问题。虽然JVM对String字符串的拼接已经进行了优化，但是假如当前的日志级别为INFO，那么这段代码所执行字符串拼接操作就是完全不必要的。因此，建议在代码加上一行日志级别的判断进行优化，如下：

```java
//非debug级别不会执行字符串拼接操作，但是debug级别会执行两次isDebugEnabled操作，性能影响不大
if(logger.isDebugEnabled()) { 
    logger.debug("Hello World To " + username);
}
```

但是，logback并不推荐在系统中使用字符串拼接的方式来输出日志，而是提倡使用参数传递的方式，由logback自己来执行日志的序列化。如下：

```java
//logger方法会判断是否为debug级别，再决定将entry序列化拼接如字符串
logger.debug("The entry is {}.", entry);
```

这种日志输出方式就无需额外包一层日志级别的判断，因为logger.debug方法内部自己会判断一次日志级别，再去执行日志内容转码的操作。注意，**传入的参数必须实现了toString方法**，不然日志在对对象进行转码时，只会打印出对象的内存地址，而不是对象中的具体内容。

### 配置文件结构

配置文件以`<configuration>`作为根元素，其下包含1个`<root>`元素用于定义根日志的配置信息，还有0到多个`<logger>`元素以及0到多个`<appender>`元素。其中`<logger>`元素对应了应用中通过LoggerFactory.getLogger()获取到的日志工具，`<appender>`元素定义了日志的输出目的地，一个`<logger>`可以关联多个`<appender>`，即允许将同样的一行日志输出到多个目的地。

#### configuration标签

作为配置文件的根标签，configuration更多的是对整个Logback配置读取的模式进行定义，configuration标签汇中可以定义的属性如下：

1. debug: 默认debug值为false，如果debug设置为true的话，则无论配置读取成功与否，都会将日志框架的状态打印出来，为false的话则只有在读取配置出错时才会打印状态日志。

2. scan：默认为false，将scan设为true的话，则logback会自动的定期扫描配置文件，如果配置文件发生变更，则logback能够快速识别并重新配置。可以通过scanPeriod来覆盖默认的扫描间隔。这个功能在生产环境建议不要开启，因为基本上生产环境的日志框架的配置都是稳定的。只有在开发环境需要调试日志框架的行为时，可以将该功能开启，减少因为修改配置进行调试而重启应用的麻烦。

#### logger标签

logger是日志流隔离的基本单位，每个logger都会绑定到一个LoggerContext。Logger之间存在树状层级关系，即A Logger可以是B Logger的父Logger。而它们之间的层级关系则是根据logger的名称来决定的。假如logger A的name为`com.moduleA`，而logger B的name为`com.moduleA.packageA`，则可以说A是B的父logger。这种树状结构的作用在于，假如B并没有定义自己的日志级别，则会继承A的日志级别。其它的如appender也会根据继承关系计算得出。

**logger只有一个name属性是必填的**。通常来说，除了需要特殊定义的几个logger name之外，其它的基本都会以module的维度进行定义，从而确保模块下的每一个类在以自己的类名获取Logger时，能够向上找到对应的Logger。

举个例子，假如现在定义了一个name为`com.rale.service的logger`，则位于`com.rale.service.HelloService.java`类中使用`LoggerFactory.getLogger(HelloService.class)`获取到的Logger，虽然在配置文件中并没有声明，但是会以该类的全路径作为logger的名称，按照Logger的层级不断向上找到最近的父Logger，并最终返回name为`com.rale.service`的logger。

logger还有一个标签为level，可以为该logger分配对应的日志级别，只有高于该级别的日志会输出。**如果没有显示定义level的值，则会从最近的显式声明了日志级别的父节点继承其日志级别**。

一个基础的logger配置如下：

```xml
<logger name="integration" level="INFO" additivity="false">
    <appender-ref ref="integration"/>
    <appender-ref ref="common-error"/>
</logger>
```

**一个logger下可以包含多个appender-ref标签，该标签声明了该logger的日志会打印到这些输出流中。这里还有一个比较特殊的属性additivity，它是用来约束appender继承行为的**。在默认情况下，aditicity的值为true，即logger除了会打印到当前显式声明的appender-ref中，还会打印到所有从父Logger中继承的appender中。例如假设root中声明了`<appender-ref ref="common">`，则integration会同时向这三个输出流中打印日志。如果父logger和子logger中存在相同的appender，该日志也会向该appender打印两遍。因此，通过additivity设置为false，可以减少因为意料之外的appender继承导致日志的过量输出。

#### appender标签

一个appender对应一个日志输出流。同一个appender可以绑定在多个logger上，即多个logger均可以向该appender输出日志。因此appender的实现内部进行了并发控制，防止日志乱码。

appender支持的输出端很多，包括控制台，文件，远程Socket服务器，MySQL，PostgreSQL等数据库，远程UNIX日志进程，JMS等。

`<appender>`有两个强制属性name和class（Appender类的全路径），包含0到多个`<layout class="">`标签，0到多个`<encoder class="">`标签，0到多个`<filter>`标签。它还可以包含任意多个Appender Bean类的成员变量属性值。

其中layout和encoder标签用来对appender中的日志进行格式化，filter标签则支持对appender中传来的日志信息进行过滤，来决定哪些日志打印哪些不打印，因此可以通过filter来定义appender维度的日志级别。

常用的appender类有`ConsoleAppender`和`RollingFileAppender`。前者用来在控制台上打印日志，后者将日志输出到文件中。

一个典型的appender如下：

```xml
    <appender name="common-error"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/sls-common-error.log</file>
        <encoder>
            <pattern>${LOCAL_FILE_LOG_PATTERN}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
    </appender>
```

这里声明了一个文件输出流，并且用file标签定义了输出文件的位置，用encoder定义了日志打印的格式。这里通过引用变量的形式来定义，变量将在后面property标签中详细介绍。接着绑定了一个filter，并且使用该filter定义了appender只会打印出日志级别大于等于ERROR级别的日志。

```xml
<!-- info级别日志会输出到test-info.log日志文件中 -->
<appender name="info-log" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>/logback/log/test-info.log</file>
    <encoder>
    	<pattern>【logbck】%blue([requestId:%X{requestId:-syslogId}]) %d{yyyy-MM-dd HH:mm:ss.SSS} %red([%thread]) %5level - %msg%n</pattern>
    </encoder>
    <!-- filter过滤器，INFO级别 -->
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
    	<level>INFO</level>
        <onMatch>ACCEPT</onMatch>
        <onMismatch>DENY</onMismatch>
    </filter>
</appender>
<!-- error级别日志会输出到test-error.log日志文件中 -->
<appender name="info-error" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>/logback/log/test-error.log</file>
    <encoder>
    	<pattern>【logbck】%blue([requestId:%X{requestId:-syslogId}]) %d{yyyy-MM-dd HH:mm:ss.SSS} %red([%thread]) %5level - %msg%n</pattern>
    </encoder>
    <!-- filter过滤器，ERROR级别 -->
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
    	<level>ERROR</level>
        <onMatch>ACCEPT</onMatch>
        <onMismatch>DENY</onMismatch>
    </filter>
</appender>
```

#### root标签

root标签要求在配置中必须声明一次，root标签其实定义的是root logger的配置信息，它的默认的日志级别为debug。所有的logger的最终的父logger一定是root logger。可以多个root标签来分别声明不同级别的日志等级输出的日志输出流。

#### property标签

property标签支持在配置文件中声明变量。配置文件的变量有四种来源，分别是通过JVM COMMAND，JAVA COMMAND，Classpth以及当前的配置文件。举个例子，JAVA命令传入变量的格式如下`java -DUSER_HOME="/home/sebastien" MyApp2`。`<property>`标签支持configuration文件中声明成员变量，它支持三种类型：KV，文件相对路径，Classpth下的文件。

```xml
  <!--键值型声明-->
  <property name="USER_HOME" value="/home/sebastien" />

  <!--配置文件声明-->
  <property file="src/main/java/chapters/configuration/variables1.properties" />

  <!--Classpath资源-->
  <property resource="resource1.properties"/>
```

对于这些变量的引用采用标准Linux变量引用方法，通过`${变量名称}`即可以引用变量的值。同样也支持为这些变量声明默认值，通过`${变量名称:-默认值}`的语法结构。

一个简单的声明配置并使用的例子如下：

```xml
<configuration>
  <property name="USER_HOME" value="/home/sebastien" />
  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${USER_HOME}/myApp.log</file>
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuration>
```

#### define标签

**define标签也是用来声明变量的，但是和上面的property的不同点在于，define声明的是动态变量**，即这些变量的值是在程序运行起来后才能得到的。比如配置文件中默认存在的`${HOSTNAME}`变量，就是通过define标签实现的，它会在程序运行后动态的获取当前所处容器的主机名，并且赋值给HOSTNAME变量。

一个典型的define标签用法如下，要求define的class中填入的类必须是PropertyDefiner接口的实现。

```xml
<configuration>

  <define name="rootLevel" class="a.class.implementing.PropertyDefiner">
    <shape>round</shape>
    <color>brown</color>
    <size>24</size>
  </define>
 
  <root level="${rootLevel}"/>
</configuration>
```

logback提供了几个基础的Definer的实现，如`FileExistsPropertyDefiner`就是用来判断path中声明的文件是否存在的一个definer。

#### include标签

include标签允许引入另一个路径下存储的logback配置，示例如下：

```xml
<configuration>
  <include file="src/main/java/chapters/configuration/includedConfig.xml"/>

  <root level="DEBUG">
    <appender-ref ref="includedConsole" />
  </root>

</configuration>
```

`src/main/java/chapters/configuration/includedConfig.xml`文件的内容如下：

```xml
<included>
  <appender name="includedConsole" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>"%d - %m%n"</pattern>
    </encoder>
  </appender>
</included>
```

要求被include进来的文件的内容必须包含在included标签内，且语法满足logback配置文件的语法。这里就是引入了includeConfig.xml中声明的一个appender。

#### rollingPolicy标签

用来设置日志的滚动策略。当达到条件后会自动将条件前的日志生成一份备份日志文件，条件后的日志输出到最新的日志文件中。常用的是按照时间来滚动（使用类`TimeBaseRollingPolicy`）还有一种就是基于索引来实现的（使用类`FixedWindowRollingPolicy`）。

```xml
    <!-- 
    通过rollingPolicy设置日志滚动的策略，这是使用按照时间滚动
    fileNamePattern属性设置滚动生成文件的格式,这里设置的精确到天，也就是按照天滚动，如果时间设置精确到秒，就按秒来滚动
    maxHistory属性设定最大的文件数，比如按天滚动，这里设置了30天，在第31天日志生成的时候，第一天的日志就会被删掉
    -->
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    	<fileNamePattern>/logback/log/test-%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
    </rollingPolicy>
```

说明：`fileNamePattern`是要格外说明的。如上我设置按天来滚动，前一天日志打印到23点59分，然后就一直没有请求日志，直到次日的1点才有新的日志进入。在0点到1点这个时间段，日志文件是不会滚动生成新的日志文件。因为**滚动的动作是需要日志写入动作来触发**。

```xml
<!-- 设置为按照索引的方式滚动，定义文件名称的时候使用%i作为占位符，滚动后会会用角标替换 -->
<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
    <fileNamePattern>/logback/log/test-%i.log</fileNamePattern>
    <minIndex>1</minIndex>
    <maxIndex>3</maxIndex>
</rollingPolicy>
<!-- 指定文件最大尺寸，达到该尺寸，就触发rollingPolicy对应的策略，maxFileSize属性指定文件大小 -->
<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
	<maxFileSize>1MB</maxFileSize>
</triggeringPolicy>
```

说明：

- maxIndex不可设置为过大，过大后会自动被置为默认值12，这个过大的值具体是多少没测试过。
- 第一次滚动，生成test-1.log，第二次滚动时会将之前的test-1.log文件修改成test-2.log，将最新生成的日志文件命名为test-1.log，依此类推，直到索引达到设置的最大值。当超过最大值的时候，会将索引最大的文件直接删除，用前一个文件移到最后一位。就像传送带，最先放上的物品会最先掉出传送带。
- 在时间滚动的方式里面，可以用时间作为触发机制。但是在使用索引方式的时候，需要定义一个triggeringPolicy作为触发机制。

**`rollingPolicy`对应的是时间策略并配合文件大小触发器来实现日志文件定义：**

```xml
<!-- 使用按照时间滚动策略，内嵌按照文件大小来分隔日志的触发器策略 -->
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    <fileNamePattern>/logback/log/test-%d{yyyy-MM-dd}-%i.log</fileNamePattern>
    <!-- 使用SizeAndTimeBasedFNATP实现，可以看一下TimeBasedRollingPolicy源码中对应timeBasedFileNamingAndTriggeringPolicy的类型，根据类型确定需要使用的class类 -->
    <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
    	<maxFileSize>1MB</maxFileSize>
    </timeBasedFileNamingAndTriggeringPolicy>
</rollingPolicy>
```

说明：按照时间滚动的单位是天，按照文件大小的滚动是1M，当今天产生的日志大小是5M，就会生成5个日志文件，每个文件对应的大小是1M，对应生成日期是一天，对应的编号不同，也就是`%i`占位符替换后的编号(从0开始)。

#### triggerPolicy标签

该标签用来设置日志触发器策略，常用的是日志大小的控制。当日志达到对应的大小的时候，就会触发。进而生成新的日志文件。日志大小的控制配合`rollingPolicy`使用的时候，不同的`rollingPolicy`会有所不同。

