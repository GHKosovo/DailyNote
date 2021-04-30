无论选择Spring的哪种数据访问方式，你都需要配置一个数据源的引用。Spring提供了在Spring上下文中配置数据源bean的多种方式，包括： 

1. 通过JDBC驱动程序定义的数据源； 
2. 通过JNDI查找的数据源； 
3. 连接池的数据源。 

对于即将发布到生产环境中的应用程序，我建议使用从连接池获取连接的数据源。如果可能的话，我倾向于通过应用服务器的JNDI来获取数据源。

## 使用JNDI数据源

Spring应用程序经常部署在Java EE应用服务器中，如WebSphere、JBoss或甚至像Tomcat这样的Web容器中。这些服务器允许你配置通过JNDI获取数据源。这种配置的好处在于数据源完全可以在应用程序之外进行管理，这样应用程序只需在访问数据库的时候查找数据源就可以了。另外，在应用服务器中管理的数据源通常以池的方式组织，从而具备更好的性能，并且还支持系统管理员对其进行热切换。

利用Spring，我们可以像使用Spring bean那样配置JNDI中数据源的引用并将其装配到需要的类中。位于jee命名空间下的<jee:jndi-lookup>元素可以用于检索JNDI中的任何对象（包括数据源）并将其作为Spring的bean。例如，如果应用程序的数据源配置在JNDI中，我们可以使用<jee:jndi-lookup>元素将其装配到Spring中，如下所示：

```xml
<jee:jndi-lookup id="dataSource"
     jndi-name="/jdbc/SpitterDS"
 resource-ref="true" />
```

其中 jndi-name属性用于指定JNDI中资源的名称。如果只设置了jndi-name属性，那么就会根据指定的名称查找数据源。但是，如果应用程序运行在Java应用服务器中，你需要将resource-ref属性设置为true，这样给定的jndi-name将会自动添加“java:comp/env/”前缀。

如果想使用Java配置的话，那我们可以借助JndiObjectFactoryBean从JNDI中查找DataSource

```java
@Bean
public JndiObjectFactoryBean dataSource() {
  JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
  jndiObjectFB.setJndiName("jdbc/SpittrDS");
  jndiObjectFB.setResourceRef(true);
  jndiObjectFB.setProxyInterface(javax.sql.DataSource.class);
  return jndiObjectFB;
}
```

## 使用数据连接池

如果你不能从JNDI中查找数据源，那么下一个选择就是直接在Spring中配置数据源连接池。尽管Spring并没有提供数据源连接池实现，但是我们有多项可用的方案，包括如下开源的实现：

1. Apache Commons DBCP (http://jakarta.apache.org/commons/dbcp)； 
2. c3p0 (http://sourceforge.net/projects/c3p0/) ； 
3. BoneCP (http://jolbox.com/) 。

这些连接池中的大多数都能配置为Spring的数据源，在一定程度上与Spring自带的DriverManagerDataSource 或 SingleConnectionDataSource很类似（我们稍后会对其进行介绍）。例如，如下就是配置DBCP BasicDataSource 的方式：

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
  p:driverClassName="org.h2.Driver"
  p:url="jdbc:h2:tcp://localhost/~/spitter"
  p:username="sa"
  p:password=""
  p:initialSize="5"
  p:maxActive="10" />
```

#### BasicDataSource的池配置属性

| 池配置属性                 | 所指定的内容                                                 |
| -------------------------- | ------------------------------------------------------------ |
| initialSize                | 池启动时创建的连接数量                                       |
| maxActive                  | 同一时间可从池中分配的最多连接数。如果设置为0，表示无限制    |
| maxIdle                    | 池里不会被释放的最多空闲连接数。如果设置为0，表示无限制      |
| maxOpenPreparedStatements  | 在同一时间能够从语句池中分配的预处理语句（prepared statement）的最大数量。如果设置为0，表示无限制 |
| maxWait                    | 在抛出异常之前，池等待连接回收的最大时间（当没有可用连接时）。如果设置为-1，表示无限等待 |
| minEvictableIdleTimeMillis | 连接在池中保持空闲而不被回收的最大时间                       |
| minIdle                    | 在不创建新连接的情况下，池中保持空闲的最小连接数             |
| poolPreparedStatements     | 是否对预处理语句（prepared statement）进行池管理（布尔值）   |

## 基于JDBC驱动的数据源

在Spring中，通过JDBC驱动定义数据源是最简单的配置方式。Spring提供了三个这样的数据源类（均位于org.springframework.jdbc.datasource包中）供选择： 

1. DriverManagerDataSource：在每个连接请求时都会返回一个新建的连接。与DBCP的BasicDataSource 不同，由 DriverManagerDataSource提供的连接并没有进行池化管理； 
2. SimpleDriverDataSource ：与 DriverManagerDataSource的工作方式类似，但是它直接使用JDBC驱动，来解决在特定环境下的类加载问题，这样的环境包括OSGi容器； SingleConnectionDataSource：在每个连接请求时都会返回同一个的连接。尽管
3. SingleConnectionDataSource不是严格意义上的连接池数据源，但是你可以将其视为只有一个连接的池。 以上这些数据源的配置与DBCPBasicDataSource的配置类似。例如，如下就是配置DriverManagerDataSource

与具备池功能的数据源相比，唯一的区别在于这些数据源bean都没有提供连接池功能，所以没有可配置的池相关的属性。 

尽管这些数据源对于小应用或开发环境来说是不错的，但是要将其用于生产环境，你还是需要慎重考虑。因为SingleConnectionDataSource有且只有一个数据库连接，所以不适合用于多线程的应用程序，最好只在测试的时候使用。而DriverManagerDataSource 和 SimpleDriverDataSource尽管支持多线程，但是在每次请求连接的时候都会创建新连接，这是以性能为代价的。鉴于以上的这些限制，我强烈建议应该使用数据源连接池。

## 使用嵌入式的数据源

除此之外，还有一个数据源是我想对读者介绍的：嵌入式数据库（embedded database）。嵌入式数据库作为应用的一部分运行，而不是应用连接的独立数据库服务器。尽管在生产环境的设置中，它并没有太大的用处，但是对于开发和测试来讲，嵌入式数据库都是很好的可选方案。这是因为每次重启应用或运行测试的时候，都能够重新填充测试数据。 

Spring的jdbc命名空间能够简化嵌入式数据库的配置。例如，如下的程序清单展现了如何使用jdbc命名空间来配置嵌入式的H2数据库，它会预先加载一组测试数据。

除此之外，还有一个数据源是我想对读者介绍的：嵌入式数据库（embedded database）。嵌入式数据库作为应用的一部分运行，而不是应用连接的独立数据库服务器。尽管在生产环境的设置中，它并没有太大的用处，但是对于开发和测试来讲，嵌入式数据库都是很好的可选方案。这是因为每次重启应用或运行测试的时候，都能够重新填充测试数据。 Spring的jdbc命名空间能够简化嵌入式数据库的配置。例如，如下的程序清单展现了如何使用jdbc命名空间来配置嵌入式的H2数据库，它会预先加载一组测试数据。

> 使用jdbc命名空间配置嵌入式数据库

```xml
<?xml version="1.0" encoding="UTF-8"?> <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/jdbc
      http://www.springframework.org/schema/jdbc/spring-jdbc-3.1.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
...

  <jdbc:embedded-
     database id="dataSource" type="H2">     <jdbc:script location="com/habum
     a/spitter/db/jdbc/schema.sql"/>     <jdbc:script location="com/habuma/sp
     itter/db/jdbc/test-data.sql"/>   </jdbc:embedded-database>
...

</beans>
```

> 其实嵌入式数据库就是提前编写好建立数据库和表的sql语句和插入数据库表数据的语句，每一次使用数据库，都是同样的数据，就是不会改变

## 使用profile选择数据源

参考文章--》Spring 4.0 高级装配的Profile章节