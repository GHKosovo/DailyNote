# 环境与Profile

> Spring从3.1版本就引入了bean profile的功能

### Profile用来干什么的？

其实就是它就是根据环境来决定该创建哪个bean，不创建哪个bean

不过Spring并不是在构建的时候做出这样的决策，而是等到运行时再来确定。这样的结果就是同一个部署单元（可能会是WAR文件）能够适用于所有的环境，没有必要进行重新构建。


## 配置profile bean

### 基于JavaConfig配置

在Java配置中，可以使用@Profile注解指定某个bean属于哪一个profile。

当@Profile注解应用在了类级别上。它会告诉Spring这个配置类中的bean只有在dev profile激活时才会创建。profile激活时才会创建。如果dev profile没有激活的话，那么带有@Bean注解的方法都会被忽略掉。

不过，从Spring 3.2开始，也可以在方法级别上使用@Profile 注解，与 @Bean注解一同使用。这样的话，就能将这两个bean的声明放到同一个配置类之中

```java
package com.myapp;
import javax.activation.DataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import
  org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import
  org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;
import org.springframework.jndi.JndiObjectFactoryBean;

@Configuration
public class DataSourceConfig {

  @Bean(destroyMethod="shutdown")
  @Profile("dev")	
  public DataSource embeddedDataSource() {
      return new EmbeddedDatabaseBuilder()
          .setType(EmbeddedDatabaseType.H2)
          .addScript("classpath:schema.sql")
          .addScript("classpath:test-data.sql")
          .build();
  }

  @Bean
  @Profile("prod")	
  public DataSource jndiDataSource() {
    JndiObjectFactoryBean jndiObjectFactoryBean =
        new JndiObjectFactoryBean();
    jndiObjectFactoryBean.setJndiName("jdbc/myDS");
    jndiObjectFactoryBean.setResourceRef(true);
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
  }

}
```

这里有个问题需要注意，尽管每个DataSource bean都被声明在一个profile中，并且只有当规定的profile激活时，相应的bean才会被创建，**但是可能会有其他的bean并没有声明在一个给定的profile范围内**。没有指定profile的bean始终都会被创建，与激活哪个profile没有关系。

### 基于XML配置

除了可以为每个环境都创建一个profile XML文件，把所有的bean定义到了同一个XML文件之中，这种配置方式与定义在单独的XML文件中的实际效果是一样的。这里有三个bean，类型都是javax.sql.DataSource，并且ID都是dataSource。但是在运行时，只会创建一个bean，这取决于处于激活状态的是哪个profile。

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:jdbc="http://www.springframework.org/schema/jdbc"
  xmlns:jee="http://www.springframework.org/schema/jee"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="
    http://www.springframework.org/schema/jee
    http://www.springframework.org/schema/jee/spring-jee.xsd
    http://www.springframework.org/schema/jdbc
    http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

  <beans profile="dev">	
    <jdbc:embedded-database id="dataSource">
      <jdbc:script location="classpath:schema.sql" />
      <jdbc:script location="classpath:test-data.sql" />
    </jdbc:embedded-database>
  </beans>

  <beans profile="qa">	
    <bean id="dataSource"
          class="org.apache.commons.dbcp.BasicDataSource"
          destroy-method="close"
          p:url="jdbc:h2:tcp://dbserver/~/test"
          p:driverClassName="org.h2.Driver"
          p:username="sa"
          p:password="password"
          p:initialSize="20"
          p:maxActive="30" />
  </beans>

  <beans profile="prod">	
     <jee:jndi-lookup id="dataSource"
                     jndi-name="jdbc/myDatabase"
                     resource-ref="true"
                     proxy-interface="javax.sql.DataSource" />
  </beans>
</beans>
```

<hr>

## 激活profile

Spring在确定哪个profile处于激活状态时，需要依赖两个独立的属性：**spring.profiles.active** 和 **spring.profiles.default**。如果设置了spring.profiles.active属性的话，那么它的值就会用来确定哪个profile是激活的。但如果没有设置spring.profiles.active属性的话，那Spring将会查找spring.profiles.default的值。如果spring.profiles.active 和 spring.profiles.default均没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在profile中的bean。

有多种方式来设置这两个属性： 

1. 作为 DispatcherServlet的初始化参数；
2. 作为Web应用的上下文参数；
3. 作为JNDI条目； 
4. 作为环境变量； 
5. 作为JVM的系统属性；
6. 在集成测试类上，使用@ActiveProfiles注解设置。

**环境变量的设置**：% export SPRING_PROFILES_ACTIVE=prod（设置SPRING_PROFILES_ACTIVE）

**JVM的设置**：

- tomcat 中 catalina.bat（.sh中不用“set”） 添加JAVA_OPS。通过设置active选择不同配置文件 set JAVA_OPTS=”-Dspring.profiles.active=test”
- eclipse 中启动tomcat。项目右键 run as –> run configuration–>Arguments–> VM arguments中添加。local配置文件不必上传git追踪管理
    -Dspring.profiles.active=”local”

#### 使用profiles进行测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes={PersistenceTestConfig.class})
@ActiveProfiles("dev")
public class PersistenceTest {
  ...
}
```

<hr>

# 条件化的bean

> spring4引入了@Conditional注解

@Conditional注解可以用到带有@Bean注解的方法上。如果给定的条件计算结果为true，就会创建这个bean，否则的话，这个bean会被忽略。

```java
@Bean
@Conditional(MagicExistsCondition.class)	
public MagicBean magicBean() {
  return new MagicBean();
}
```

> 从Spring 4开始，@Profile注解进行了重构，使其基于@Conditional 和 Condition实现。
>

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Documented
@Conditional(ProfileCondition.class)
public @interface Profile {
  String[] value();
}
```

# 处理自动装配的歧义性

在第2章中，我们已经看到如何使用自动装配让Spring完全负责将bean引用注入到构造参数和属性中。自动装配能够提供很大的帮助，因为它会减少装配应用程序组件时所需要的显式配置的数量。

仅有一个bean匹配所需的结果时，自动装配才是有效的。如果不仅有一个bean能够匹配结果的话，这种歧义性会阻碍Spring自动装配属性、构造器参数或方法参数。

比如以下的例子：

```java
@Autowired
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

```java
@Component
public class Cake implements Dessert { ... }
@Component
public class Cookies implements Dessert { ... }
@Component
public class IceCream implements Dessert { ... }
```

但是，当确实发生歧义性的时候，Spring提供了多种可选方案来解决这样的问题。你可以将可选bean中的某一个**设为首选（primary）的bean**，或者**使用限定符（qualifier）**来帮助Spring将可选的bean的范围缩小到只有一个bean。

#### 标示首选的bean

```java
@Component
@Primary
public class IceCream implements Dessert { ... }
```

#### 限定自动装配的bean

```java
@Autowired
@Qualifier("iceCream")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

这是使用限定符的最简单的例子。为@Qualifier注解所设置的参数就是想要注入的bean的ID。所有使用@Component注解声明的类都会创建为bean，并且bean的ID为首字母变为小写的类名。因此，@Qualifier("iceCream")指向的是组件扫描时所创建的bean，并且这个bean是IceCream类的实例。 

实际上，还有一点需要补充一下。更准确地讲，@Qualifier("iceCream")所引用的bean要具有String类型的“iceCream”作为限定符。如果没有指定其他的限定符的话，所有的bean都会给定一个默认的限定符，这个限定符与bean的ID相同。因此，框架会将具有“iceCream”限定符的bean注入到setDessert()方法中。这恰巧就是ID为iceCream的bean，它是IceCream类在组件扫描的时候创建的。

> 基于默认的bean ID作为限定符是非常简单的，但这有可能会引入一些问题。如果你重构了IceCream类，将其重命名为Gelato的话，那此时会发生什么情况呢？如果这样的话，bean的ID和默认的限定符会变为gelato，这就无法匹配setDessert()方法中的限定符。自动装配会失败。

#### 创建自定义的限定符

ID作为限定符。在这里所需要做的就是在bean声明上添加@Qualifier注解。例如，它可以与@Component组合使用，如下所示：

```java
@Component
@Qualifier("cold")
public class IceCream implements Dessert { ... }
```

在这种情况下，cold限定符分配给了IceCreambean。因为它没有耦合类名，因此你可以随意重构IceCream的类名，而不必担心会破坏自动装配。在注入的地方，只要引用cold限定符就可以了

```java
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

#### 使用自定义的限定符注解

如果需要两个限定符才能定位到相关的bean，可行吗？

```java
@Component
@Qualifier("cold")
@Qualifier("fruity")
public class Popsicle implements Dessert { ... }
```

答案是不可行：Java不允许在同一个条目上重复出现相同类型的多个注解。[1]如果你试图这样做的话，编译器会提示错误。在这里，使用@Qualifier注解并没有办法（至少没有直接的办法）将自动装配的可选bean缩小范围至仅有一个可选的bean。

那就自己创建自定义的限定符注解咯，比如下面的**@Cold**注解

```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD,
         ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Cold { }
```

<hr>

## bean的作用域

在默认情况下，Spring应用上下文中所有bean都是作为以单例（singleton）的形式创建的。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。 

在大多数情况下，单例bean是很理想的方案。初始化和垃圾回收对象实例所带来的成本只留给一些小规模任务，在这些任务中，让对象保持无状态并且在应用中反复重用这些对象可能并不合理。

 有时候，可能会发现，你所使用的类是易变的（mutable），它们会保持一些状态，因此重用是不安全的。在这种情况下，将class声明为单例的bean就不是什么好主意了，因为对象会被污染，稍后重用的时候会出现意想不到的问题。 

Spring定义了多种作用域，可以基于这些作用域创建bean，包括： 

1. 单例（Singleton）：在整个应用中，只创建bean的一个实例。 
2. 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例。 
3. 会话（Session）：在Web应用中，为每个会话创建一个bean实例。 
4. 请求（Rquest）：在Web应用中，为每个请求创建一个bean实例。

单例是默认的作用域，但是正如之前所述，对于易变的类型，这并不合适。如果选择其他的作用域，要使用**@Scope**注解，它可以与**@Component** 或 **@Bean**一起使用。

> @Scope同时还有要给proxyMode属性，如需了解详情，请查阅 -- [美] Craig Walls. Spring实战（第4版） (Kindle 位置 1604-1606). 人民邮电出版社. Kindle 版本. 
>

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Notepad { ... }
```

使用ConfigurableBeanFactory 类的 SCOPE_PROTOTYPE常量设置了原型作用域。你当然也可以使用@Scope("prototype")，但是使用SCOPE_PROTOTYPE常量更加安全并且不易出错。

```java
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Notepad notepad() {
  return new Notepad();
}
```

> 原型bean配置 

```xml
<bean id="notepad"
      class="com.myapp.Notepad"
      scope="prototype" />
```

> xml配置方式

<hr>

## 运行时值的注入

当讨论依赖注入的时候，我们通常所讨论的是将一个bean引用注入到另一个bean的属性或构造器参数中。它通常来讲指的是将一个对象与另一个对象进行关联。 

但是bean装配的另外一个方面指的是将一个值注入到bean的属性或者构造器参数中。

可以使用硬编码的形式，就是直接注入值，但有的时候，我们可能会希望避免硬编码值，而是想让这些值在运行时再确定。为了实现这些功能，Spring提供了两种在运行时求值的方式：

- 属性占位符（Property placeholder）

- Spring表达式语言（SpEL）

很快你就会发现这两种技术的用法是类似的，不过它们的目的和行为是有所差别的。

### 注入外部的值

在Spring中，处理外部值的最简单方式就是声明属性源并通过Spring的Environment来检索属性。

```java
package com.soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;

@Configuration
@PropertySource("classpath:/com/soundsystem/app.properties")	
 public class ExpressiveConfig {

  @Autowired
  Environment env;

  @Bean
  public BlankDisc disc() {
    return new BlankDisc(
        env.getProperty("disc.title”),	
         env.getProperty("disc.artist"));
  }

}
```

在上面的例中，@PropertySource引用了类路径中一个名为app.properties的文件。它大致会如下所示： 这个属性文件会加载到Spring的Environment中，稍后可以从这里检索属性。同时，在disc()方法中，会创建一个新的BlankDisc，它的构造器参数是从属性文件中获取的，而这是通过调用getProperty()

#### 深入学习Spring的Environment

其实getProperty()方法有四个重载的变种形式：

- String getProperty(String key)
- String getProperty(String key, String defaultValue)
- T getProperty(String key, Class<T> type)
- T getProperty(String key, Class<T> type, T defaultValue)

> 详情请看-->[美] Craig Walls. Spring实战（第4版） (Kindle 位置 1660-1661). 人民邮电出版社. Kindle 版本. 
>

当然Environment还提供了一些跟属性相关的方法和用来检查哪个profile处于激活状态的方法，详细章节在[Spring 实战（第4版）第三章 3.5小节-运行时值注入-注入外部的值]有详细记载

#### 解析属性占位符

Spring一直支持将属性定义到外部的属性的文件中，并使用占位符值将其插入到Spring bean中。在Spring装配中，占位符的形式为使用“${ ... }”包装的属性名称。

```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="${disc.title}"
      c:_artist="${disc.artist}" />
```

如果我们依赖于组件扫描和自动装配来创建和初始化应用组件的话，那么就没有指定占位符的配置文件或类了。在这种情况下，我们可以使用@Value注解，它的使用方式与@Autowired注解非常相似。比如，在BlankDisc类中，构造器可以改成如下所示：

```java
public BlankDisc(
      @Value("${disc.title}") String title,
      @Value("${disc.artist}") String artist) {
  this.title = title;
  this.artist = artist;
}
```

**为了使用占位符**，我们必须要配置一个PropertyPlaceholderConfigurer bean或PropertySourcesPlaceholderConfigurer bean。从Spring 3.1开始，推荐使用PropertySourcesPlaceholderConfigurer，因为它能够基于Spring Environment及其属性源来解析占位符。 

如下的 @Bean方法在Java中配置了PropertySourcesPlaceholderConfigurer ： 

```java
@Bean
public
static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
  return new PropertySourcesPlaceholderConfigurer();
}
```

如果你想使用XML配置的话，Spring context命名空间中的\<context:propertyplaceholder>元素将会为你生成PropertySourcesPlaceholderConfigurer bean：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">

  <context:property-placeholder />

</beans>
```

解析外部属性能够将值的处理推迟到运行时，但是它的关注点在于根据名称解析来自于Spring Environment和属性源的属性。而Spring表达式语言提供了一种更通用的方式在运行时计算所要注入的值。

#### 使用Spring表达式语言进行装配

Spring 3引入了Spring表达式语言（Spring Expression Language，SpEL），它能够以一种强大和简洁的方式将值装配到bean属性和构造器参数中，在这个过程中所使用的表达式会在运行时计算得到值。

SpEL拥有很多特性，包括：

1. 使用bean的ID来引用bean；
2. 调用方法和访问对象的属性；
3.  对值进行算术、关系和逻辑运算； 
4. 正则表达式匹配；
5.  集合操作。

需要了解的第一件事情就是**SpEL表达式**要放到“**#{ ... }**”之中，这与属性占位符有些类似，属性占位符需要放到“**${ ... }**”之中。下面所展现的可能是最简单的SpEL表达式了：

如果通过组件扫描创建bean的话，在注入属性和构造器参数时，我们可以使用@Value注解，这与之前看到的属性占位符非常类似。不过，在这里我们所使用的不是占位符表达式，而是SpEL表达式。例如，下面的样例展现了BlankDisc，它会从系统属性中获取专辑名称和艺术家的名字：

```java
public BlankDisc(
      @Value("#{systemProperties['disc.title']}") String title,
      @Value("#{systemProperties['disc.artist']}") String artist) {
  this.title = title;
  this.artist = artist;
}
```

在XML配置中，你可以将SpEL表达式传入<property> 或 <constructor-arg> 的 value属性中，或者将其作为p-命名空间或c-命名空间条目的值。例如，在如下BlankDisc

```xml
<bean id="sgtPeppers"
      class="soundsystem.BlankDisc"
      c:_title="#{systemProperties['disc.title']}"
      c:_artist="#{systemProperties['disc.artist']}" />
```

SpEL表达式也可以引用其他的bean或其他bean的属性。例如，如下的表达式会计算得到ID为sgtPeppers的bean的artist

```java
#{sgtPeppers.artist}
```

我们还可以通过systemProperties对象引用系统属性：

```java
#{systemProperties['disc.title']}
```

如果要在SpEL中访问类作用域的方法和常量的话，要依赖T()这个关键的运算符。例如，为了在SpEL中表达Java的Math类，需要按照如下的方式使用T() 运算符：

```java
T(java.lang.Math)
```

##### SpEL运算符

| 运行符类型 | 运算符                                                 |
| ---------- | ------------------------------------------------------ |
| 算数运算   | + 、 - 、 * 、 / 、 % 、 ^                             |
| 比较运算   | < 、 > 、 == 、 <= 、 >= 、 lt 、 gt 、 eq 、 le 、 ge |
| 逻辑运算   | and 、 or 、 not 、 │                                  |
| 条件运算   | ?: (ternary) 、 ?: (Elvis)                             |
| 正则表达式 | matches                                                |

> 具体详细章节在 [Spring 实战（第4版）第三章 3.5小节-运行时值注入-使用Spring表达式语言进行装配] 有详细记载

[美] Craig Walls. Spring实战（第4版） 人民邮电出版社. Kindle 版本. 