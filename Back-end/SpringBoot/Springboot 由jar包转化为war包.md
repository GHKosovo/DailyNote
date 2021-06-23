springboot内置了tomcat，故不需要我们额外配置tocmat服务器，但是有时这也可能是我们的一个瓶颈，因为如果我们需要对tomcat做集群或者一些优化的话是非常不便的，所以我们仍然需要将springboot的项目部署到外在的tomcat中；

## 步骤

第一步：将Springboot的项目打包方式设置为war

```xml
<groupId>com.example</groupId>
<artifactId>demo</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>war</packaging>
```

第二步：移除内嵌的tomcat模块，但是为了在本机测试方便，还是需要引入它，配置如下

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```

> 添加tomcat-servlet-api模块，我不用添加也是可以运行的

```xml
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>tomcat-servlet-api</artifactId>
    <version>7.0.42</version>
    <scope>provided</scope>
</dependency>
```

第三步：修改入口方法，继承一个SpringBootServletInitializer类，并且覆盖configure方法

```java
package com.lj.wx;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

@SpringBootApplication
public class WxApplication extends SpringBootServletInitializer {

    public static void main(String[] args) {
        SpringApplication.run(WxApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(this.getClass());
    }
}
```

第四步：添加war插件，用来自定义打包以后的war包名称

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.0.2</version>
    <!--<configuration>
        <warSourceExcludes>src/main/resources/**</warSourceExcludes>
        <warName>springBoot</warName>
    </configuration>
	-->
</plugin>
```

引入上面这个插件，是避免maven打包的时候为我们默认的一个带有版本号的war包名称，因为我们部署到tomcat以后，在访问项目的时候，需要用到这个war包的名称。例如的war包名称为springBoot。

本地访问的路径为：http://localhost:9090/users/。

则部署到tomcat以后访问路径为：http://localhost:9090/springBoot/users/。

## 遇到问题 

如果有问题，直接查看日志，因为日志会给我们很多的提示，对于我们解决错误会有很大的帮助。不要一味的根据自己的猜测来解决问题，这样会让我们走很多的弯路。

tomcat的日志有四种：

- catalina.2021.06.17.log
- localhost.2021.06.17.log
- host-manager.2021.06.17.log
- localhost_access_log.2021-06.17.txt

> 我在linux上部署tomcat后，可能还会有个catalina.out日志文件

一般看前第一种和第二种就可以了，如果有catalina.out，则直接看catalina.out就可以直接发现问题。