## 概述

SpringBoot项目创建后，便可以直接使用日志。默认打印在控制台。但有时我们需要自定义日志的级别，甚至希望不同包输出不同的日志级别。或者希望将日志信息保存到文件中，方便存档 

## 要求

必须定义一个文件名为`logback-spring.xml`或`logging.config`，然后在其内部设置，放在类路径下。

## 模板一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">

    <!--property用于配置变量，可通过${LOG_PATH} 取对应的值-->
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_PATH" value="G:/iotLog2"/>
    <property name="PATTERN" value=""/>
    
    <contextName>logback</contextName>

    <!--输出到控制台，一个appender定义一种输出策略，可以定义多个-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!--filter中定义要输出的日志级别，例如：输出info级别以上的日志，默认是info-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>info</level>
        </filter>
        <!--日志输出编码格式化-->
        <encoder>
            <pattern>
             %d{yyyy-MM-dd} = [%thread] = %-5level = %logger{50} = %msg%n
            </pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>


    <!-- 输出到文件info，日期滚动记录 -->
 	<appender name="logInfoFile"
           class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--<Prudent>true</Prudent>-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--过滤 其他只留info-->
            <level>info</level>
            <!--匹配到就禁止-->
            <onMatch>ACCEPT</onMatch>
            <!--没有匹配到就允许-->
            <onMismatch>DENY</onMismatch>
        </filter>
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy 每天会生成一个日志文件-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式,以防止日志填满整个磁盘空间-->
            <fileNamePattern>
                ${LOG_PATH}/info/iot-info-%d{yyyy-MM-dd}.log
            </fileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>
            <!--用来指定日志文件的上限大小，那么到了这个值，就会删除旧的日志-->
            <!--<totalSizeCap>1GB</totalSizeCap>-->
        </rollingPolicy>

        <append>true</append>

        <!--日志输出编码格式化-->
        <encoder>
            <charset>GBK</charset>
            <pattern>${PATTERN}</pattern>
        </encoder>
    </appender>

    <!--把上边的两种种输出策略添加到跟节点，名称要保证和上边一致，否则报错 ，并指明输出级别为INFO-->
    <root level="info">
        <appender-ref ref="console"/>
        <appender-ref ref="logInfoFile"/>
    </root>

    <!--可选节点，用来具体指明包的日志输出级别  如果不设置，则会交个root配置的appender处理-->
    <!--将该包下的日志交给console的appender处理，additivity设为false表示不再向上传递，如果置为true,则root接到后会再打印一次。 -->
    <logger name="com.iot.mqtt.controller" level="INFO" additivity="false">
    	<appender-ref ref="console"/>
    </logger>

</configuration>
```

## 节点详细介绍

**configuration**：跟节点有以下属性

- scan: 此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true
- scanPeriod: 设置监测配置文件是否有修改的时间间隔,当scan为true时生效
- debug: 当此属性设置为true时，将打印出logback内部日志信息.默认false

**contextName**: 设置日志上下文名称，可以通过`%contextName`来打印日志上下文名称

**property**： 可以用来定义变量，可以通过`${name}`来访问，有以下三个属性

- name：名称
- value：值
- file: 用于指定配置文件的路径，如果你有多个配置信息，可以写在配置文件中，然后通过file引入

**appender:** 格式化日志输出节点,可以配置多个。有两个属性name和class.
name可以自定义，class指定输出策略，一般就是控制台和文件输出。apeender有以下子节点.

- filter: 日志输出拦截器,一般使用系统提供的即可，祥见配置。

- encoder： 和pattern节点组合用于具体输出的日志格式和编码方式。

- file: 节点用来指明日志文件的输出位置，可以是绝对路径也可以是相对路径

- rollingPolicy: 日志回滚策略，在这里我们用了TimeBasedRollingPolicy，基于时间的回滚策略,有以下子节点fileNamePattern，必要节点，可以用来设置指定时间的日志归档，例如我们上面的例子是每天将日志归档成一个zip包

- maxHistory : 可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件,，例如设置为30的话，则30天之后，旧的日志就会被删除

- totalSizeCap: 可选节点，用来指定日志文件的上限大小，例如设置为3GB的话，那么到了这个值，就会删除旧的日志

**root节点:** 必选节点，用来指定最基础的日志输出级别，必须将定义的appender添加到该节点

**logger节点**:可选节点，用来具体指明包的日志输出级别，它将会覆盖root的输出级别

## 模板二

使用该配置会生成两个文件夹 info和error，分别存放info和error级别的日志文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="60 seconds" debug="false">

    <!--property用于配置变量，可通过${LOG_PATH} 取对应的值-->
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_PATH" value="G:/iotLog"/>
    <property name="PATTERN"
              value="%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n"/>
    <property name="CONSOLE_LOG_PATTERN"
              value="%date{yyyy-MM-dd HH:mm:ss} | %highlight(%5p) | %green(%thread) | %boldMagenta(%logger) | %cyan(%msg%n)"/>

    <contextName>logback</contextName>

    <!--输出到控制台，一个appender定义一种输出策略，可以定义多个-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!--filter中定义要输出的日志级别，例如：输出info级别以上的日志，默认是info-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>info</level>
        </filter>
        <!--日志输出编码格式化-->
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>


    <!-- 输出到文件info，日期滚动记录 -->
    <appender name="logInfoFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--<Prudent>true</Prudent>-->
        <!--如果只是想要 Info 级别的日志，只是过滤 info 还是会输出 Error 日志，因为 Error 的级别高，所以我们使用下面的策略，可以避免输出 Error 的日志-->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--过滤 其他只留info-->
            <level>info</level>
            <!--匹配到就禁止-->
            <onMatch>ACCEPT</onMatch>
            <!--没有匹配到就允许-->
            <onMismatch>DENY</onMismatch>
        </filter>
        <!--日志名称，如果没有File 属性，那么只会使用FileNamePattern的文件路径规则
               如果同时有<File>和<FileNamePattern>，那么当天日志是<File>，明天会自动把今天
               的日志改名为今天的日期。即，<File> 的日志都是当天的。-->
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy 每天会生成一个日志文件-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
            <fileNamePattern>${LOG_PATH}/info/iot-info-%d{yyyy-MM-dd}.log</fileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>
            <!--用来指定日志文件的上限大小，那么到了这个值，就会删除旧的日志-->
            <!--<totalSizeCap>1GB</totalSizeCap>-->
        </rollingPolicy>

        <append>true</append>

        <!--展示格式 layout-->
        <!--<layout class="ch.qos.logback.classic.PatternLayout">-->
            <!--<Pattern>${CONSOLE_LOG_PATTERN}</Pattern>-->
        <!--</layout>-->
        <!--日志输出编码格式化-->
        <encoder>
            <charset>GBK</charset>
            <pattern>${PATTERN}</pattern>
        </encoder>
    </appender>


    <!--输出到文件error，日期滚动记录 -->
    <appender name="logErrorFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <!--只记录error-->
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <FileNamePattern>${LOG_PATH}/error/iot-error-%d{yyyy-MM-dd}.log</FileNamePattern>
            <maxHistory>90</maxHistory>
        </rollingPolicy>

        <!--追加记录-->
        <append>true</append>

        <encoder>
            <charset>GBK</charset>
            <pattern>${PATTERN}</pattern>
        </encoder>
    </appender>


    <!--<root level="info,debug">-->
        <!--<appender-ref ref="console"/>-->
        <!--<appender-ref ref="logFile"/>-->
    <!--</root>-->

    <!--可选节点，用来具体指明包的日志输出级别 大小写无关-->
    <logger name="com.iot.mqtt.controller" level="INFO" additivity="false"/>


    <!--开发环境-->
    <springProfile name="dev">
        <!--必选节点，用来指定最基础的日志输出级别-->
        <root level="DEBUG">

            <appender-ref ref="logInfoFile"/>

            <appender-ref ref="logErrorFile"/>

            <appender-ref ref="console"/>

            <!--<appender-ref ref="DBAPPENDER"/>-->

        </root>

    </springProfile>

    <!--生产环境-->
    <springProfile name="pro">
        <!--必选节点，用来指定最基础的日志输出级别-->
        <root level="INFO">
            <appender-ref ref="logInfoFile"/>

            <appender-ref ref="logErrorFile"/>

            <appender-ref ref="console"/>

            <!--<appender-ref ref="DBAPPENDER"/>-->
        </root>

    </springProfile>








    <!--日志异步到数据库  -->

    <!--<appender name="DBAPPENDER" class="ch.qos.logback.classic.db.DBAppender">-->

    <!--<connectionSource class="ch.qos.logback.core.db.DataSourceConnectionSource">-->

    <!--<dataSource class="com.zaxxer.hikari.HikariDataSource">-->

    <!--<driverClassName>com.mysql.jdbc.Driver</driverClassName>-->

    <!--<jdbcUrl>jdbc:mysql://localhost:3306/albedo-new?useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=false</jdbcUrl>-->

    <!--<username>root</username>-->

    <!--<password>123456</password>-->

    <!--<poolName>HikariPool-logback</poolName>-->

    <!--</dataSource>-->

    <!--</connectionSource>-->

    <!--&lt;!&ndash; 此日志文件只记录info级别的 &ndash;&gt;-->

    <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->

    <!--<level>warn</level>-->

    <!--<onMatch>ACCEPT</onMatch>-->

    <!--<onMismatch>DENY</onMismatch>-->

    <!--</filter>-->

    <!--&lt;!&ndash; 此日志文件只记录info级别的 &ndash;&gt;-->

    <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->

    <!--<level>error</level>-->

    <!--<onMatch>ACCEPT</onMatch>-->

    <!--<onMismatch>DENY</onMismatch>-->

    <!--</filter>-->

    <!--</appender>-->

</configuration>
```

详情可看Spring[官方文档](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-logging.html#boot-features-logback-extensions)或logback[官方文档](http://logback.qos.ch/codes.html)

#抄自[freesOcen的博客](https://blog.csdn.net/gexiaoyizhimei/article/details/93907932)

