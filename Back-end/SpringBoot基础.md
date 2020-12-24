## 基础 

### 声明包

虽然SpringBoot没有要求使用包，但是最好声明一些包，因为如果没有声明包，那么SpringBoot会使用默认的包，当使用某些注解（`@ComponentScan`, `@ConfigurationPropertiesScan`, `@EntityScan`, or `@SpringBootApplication`）的时候可能会出现问题，因为来自每个jar包的每个类都会被读取

### 定位主程序类

在其他类的根包上定义主应用程序，这样，主类上`@SpringBootApplication`就能够隐式地搜寻这些包下的类

而`@SpringBootApplication`可以用  `@EnableAutoConfiguration` and `@ComponentScan` 来代替

也就是说这个注解起到**自动配置**和**搜寻包下类**的作用

### 配置类

给类加上注解`@Configuration`这个类就被声明为配置类

`@Import`可以用来引入配置类；而`@ComponentScan`不仅可以获取所有的**spring组件**，还包括`@Configuration`这些配置类

如果想引入xml的配置，可以使用`@ImportResource`来引入xml配置文件

### 自动配置

SpringBoot的自动配置是根据引入的包为基础尝试自动配置，不需要去写bean;

具体方式是：选择添加`@EnableAutoConfiguration`或`@SpringBootApplication`到一个注解了`@Configuration`的类中以此完成自动配置

> 自动配置是非入侵式的，如果自己配置了内容，那么自动配置将失效；通过`debug`形式启动应用，可以看到哪些自动配置被应用了。

在`@SpringBootApplication`注解中添加`exclude`属性可以关闭某个自动配置类；如果这个类不在类路径下， 则使用`excludeName`属性，当然，在`EnableAutoCOnfiguration`注解下这两个属性也同样生效。如果需要关闭的自动配置类比较多，则可以在属性文件中声明`spring.autoconfigure.exclude`来关闭更多自动配置类

### Spring beans和依赖注入

一般我们都`@CompannetScan`来查找bean，用`@Autowired`来做构造函数注入；

对于`@CompannetScan`注解，如果没有赋予参数给他，那么所有的应用组件(`@Component`, `@Service`, `@Repository`, `@Controller` etc.)都会被自动注册为Spring Beans；

而如果bean里面有某个bean的构造函数，则可以忽略这个bean的依赖注入注解

### 使用@SpringBootApplication注解

这个`@SpringBootApplication`相当于同时使用了三个注解：

- `@EnableAutoConfiguration`: enable [Spring Boot’s auto-configuration mechanism](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-auto-configuration)
- `@ComponentScan`: enable `@Component` scan on the package where the application is located (see [the best practices](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-structuring-your-code))
- `@Configuration`: allow to register extra beans in the context or import additional configuration classes

`@ComponentScan`可以用`@import`代替来引入特定的bean，`@EnableAutoConfiguration` and `@ComponentScan`其实都可以使用自定义来代替

### 使用Developer Tools来快速开发

#### 默认属性

#### 自动重启

#### 在线加载

#### 全局设置

#### 远程应用

### 问题

#### SpringBoot为什么能创建一个可执行的jar文件?

这个其实跟`pom.xml`文件中的两个部分有关，一部分就是maven插件，也就是`spring-boot-maven-plugin`,另一方部分则是使用了parent POM

#### @EnableAutoConfiguration到底有什么用？

SpringBoot项目的自动配置不是随系统启动吗？怎么还需要开启阿？

## 获取准备就绪的产品的特点

### EndPoint

通过导入`actuator`依赖，可以通过**HTTP**或**JMX**的方式来获取项目的一些详细信息。

### 通过HTTP监控和管理应用

### 通过JMX监控和管理应用

