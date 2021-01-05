## SSM框架涉及到的知识点

1. **group id 和 artifact id**

   创建maven项目，group id 和 artifact id都是必须要填写的，因为项目位置就是通过这两个id去查找的，group id 一般分为多个段，第一段为域，第二段为公司名称（这里就说两段而已）。域又分为org、com、cn等；

   而artifact id 一般为项目名称

2. **facet 定义了 java ee 项目的特性和要求**

   一般创建一个maven项目，都没有web.xml文件，这是因为该项目还不是一个动态web项目，需要指定一下其特性，也就是下面得Project facets属性

3. **applicationContext配置**

   属性配置中可用value来获取一些相对目录的数值或文件

4. **\<mvc:annotation-driven>**

   \<mvc:annotation-driven>标签其实就是分发请求给controller方法，它能够映射请求URL和参数给响应的controller方法。

5. **\<context:component-scan>**

   \<context:component-scan>则是扫描指定包下的类并注册这些包下的被注解标记的组件为Spring的bean

6. **jdbcUrl**

   `JDBC URL: jdbc:mysql://<host><port>/<database_name>`

   默认端口3306，如果服务器使用默认端口则port可以省略

   MySQL Connector/J Driver 允许在URL中添加额外的连接属性

   `jdbc:mysql://<host>:<port>/<database_name>?property1=value1&property2=value2`

7. **SqlSessionFactoryBean**

   正如SqlSessionFactoryBean的名字，它是生产SqlSessionFactory的工厂Bean；SqlSessionFactory是一种生产SqlSession的工厂；SqlSession是代表数据库连接客户端和数据库Server之间的会话信息；SqlSession实现发送sql语句，管理事务和获取mapper；

   SqlSession是关联到具体数据库连接的，但是如果每次创建和销毁都直接操作物理连接的话，那么这个资源浪费很高，效率很低。

   使用数据库连接池时，关闭SqlSession实例 ，其实只是把数据库连接对象（代表物理资源）放回到对象池中，并没有直接销毁，使用连接池技术极大提高了物理资源利用率，缩减了创建物理连接所需的时间、资源等等

   相关知识点出处[在这里](https://www.jianshu.com/p/f19a58938959)

8. **关于mapper映射的详解**

   还不是很懂接口和mapper映射之间如何做到关联的

   通过 [Mybatis从入门到精通](Mybatis从入门到精通.md) 可大概了解。dao层的接口和mapper映射是通过动态代理来实现的。只要在SpringIoC容器中实现mapperScannerConfigurer类就行，这个mapper其实解释起来比较复杂，因为spring帮忙做了很多事，首先就是调用接口可以直接调用到mapper.xml里面的数据库语句；这个就是mybatis里面的动态代理；但是mapper接口要实现这个功能，还需要实现类，这个实现类可以获取到sqlsessionfactory来获取session后调用mapper接口，然后mapperScannerConfigurer可以一次性扫描多个接口，并实现这个接口生成实现类

   参考文档在[这里](http://mybatis.org/spring/zh/mappers.html)

9. **事务管理/声明式事务管理**

   事务管理需要数据库的连接，所以一定要导入数据源；

   声明式事务管理有两种，一种是基于tx和aop命名空间的xml配置文件；一种是基于@Transactional注解

   相关参考文档在[这里](https://blog.csdn.net/donggua3694857/article/details/69858827)

10. **aop和tx在事务方面的理解**

    对于tx在applicationContext中出现，我一直不理解它是何物，现在知道了

    \<tx:advice>其实就是aop的通知，而\<tx:attributes>和\<tx:method>其实就是针对service里面的方法设置的事务管理属性，也不知道对不对。。。。。**待续！！！**

    我觉得是对的，哭了:sob::sob:

11. **\<aop:poincut>**中的**expresstion**

    比如这一个`execution(* cn.lj.service..*(..))`，第一个`*`表示返回值，这里表示任意；然后是包service；然后是service下类的任意方法，就是那个`*`，最后`(..)`是所有参数

    最靠近`(..)`的为方法名,靠近`.\*(..))`的为类名或者接口名,如上例的JoinPointObjP2.*(..))

    [相关内容链接](https://blog.csdn.net/kkdelta/article/details/7441829)

12. **启动项目失败，无法找到applicationContext.xml**

    我觉得很奇怪，刚开始是因为resources文件夹没写对，然后修改以后还是不行，最后发现，我的项目之前是jar	类型而不是war类型，所以才一直不成功的

13. **Spring Aop**

    \<aop:aspect>是面向切面编程；而\<aop:advisor>则是进行事务管理，但二者的实现原理都是一样的

14. **\<mvc:annotation-driven>**

    这个东西是干嘛用的？其实讲起来挺麻烦的，简单点就是实现请求的映射，也就是完成对requestmapping这个注解的实现，同时还有请求参数等的设置（比如:@param）都是通过它来实现

15. **java properties file**

    我想通过`<context:property-placeholder />`动态引入一些数据库连接属性，但是一直报错，无法解析；最后发现原来是我的文件名写错了，因为一般java的属性文件都是以“properties”结尾的，但是我拼措了；

    **！！！怎么说，发现spring里面很多配置的东西都需要特别注意名字**

16. **Spring单元测试**

    使用spring的测试模块，需要导入spring-test包

17. **单元测试Mybatis**

    记得在SpringIoC容器中添加mapperscannerconfigurer这个bean，不然无法使用dao层里面的接口，因为`<context:component-scan>`扫描不包括dao层。

18. **@Autowired**

    spring可以实现控制反转，就是实例化对象，但是如果对象里面有属性呢？那就用注解@autowired 直接注入属性

19. **bean作用域singleton和prototype的区别**

    singleton：容器在初始化时，就会创建对象；单例也支持延迟加载（就是只有在请求时再生成）；

    prototype：容器在初始化时，不创建对象，只是在每次使用时（每次容器获取对象时）再创建对象；并且每次获取对象都会创建一个新的对象；

20. **Bean的生命周期不同于IoC容器等的生命周期**

    Bean的生命周期：创建---初始化（实例化）---销毁

    容器生命周期：初始化---使用---销毁

21. **取余方法可以用来筛选数**

    比如筛选“小于20”的数，就可以用取余方法，我是在使用任意对象取值时遇到的；如下：int value = random.nextInt()%20

22. **random对象**

    取任意值的时候，放在方法里面的数,比如value，只会取“0到value-1”这个范围的数。

23. **graphics对象**（图形上下文）

    graphics是可以用来对几何图形的坐标转换，颜色管理和文本布局的操作的。比如在里面画线画点写字都可以，我是用来构建验证码的。image通过方法getGraphics()可获取到这个对象

24. **bufferimage对象**

    这个对象只是image对象的子类，但是带有缓冲区，可以自由的操作修改，一般通过getGraphics()获取到图形的上下文对象后进行各个方面的修改。

25. **上传文件**--(相关包:common-fileupload.jar)

    请求request里面有multipartFiles，但是，一直取不到里面的值；然后通过debug看，requestl里面不止有它呢，还有其他很多。通过封装对象，把multipartFile封装到对象newuser中的属性(List<MultipartFile>)files中，然后返回newuser值后调用它的方法获取files[0]，就可以获取里面MultipartFile类型的值;

    关于之前request里面的值,仔细查看这个request，发现它是DefaultMultipartHttpServletRequest,所以只要把请求改为它，就可以获取到里面的MultipartFiles了

26. **上传下载路径设置**

    利用请求`request.getServletContext().getRealPath("/upload/");`获取到得这个文件路径很奇怪，结果是`C:\Users\llj\eclipse-workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps\demo2\upload\`所以我当初在项目里面找是找不到的，后面使用String直接设置，简单明了；

27. **下载要点**

    下载比上传复杂点，都要设置ContentType和请求头的Content-Disposition属性，有两种方法：

    法一：直接在响应中设置文件，需要使用到这个方法*Files.copy(要下载的文件的路径,响应的输出流)*

    法二：（使用Spring框架）返回值为ResponseEntity<byte[]> 

28. **spring配置redis**

    使用1.8版本及以下版本，配置redistemplate时，用jedisConnectionFactory时，可以直接配置ip和port，poolconfig;但是高于1.8版本我就找不到这个东西了

29. **关于maven pom文件中 scope的分类**

    网友关于这些分类的[解释](https://www.cnblogs.com/molao-doing/articles/Maven.html)

    - 默认是**compile**，表示被依赖需要参与当前项目的编译，测试、运行也一样参与其
    - **runtime**表示依赖包无需参与项目的编译，但是测试和运行周期参与其中。（也就是跳过编译）
    - **test**表示在一般的编译和运行时都不需要，只有在测试编译和运行阶段才用到。
    - **provided**表示该依赖已经提供，旨在未提供时才被使用。
    - **system**表示该依赖不会从maven仓库下载，而是从本地系统指定路径下寻找。
    - **import**仅支持在`<dependencyManagement>`中的类型依赖项上使用。表示要在指定POM中用有效的依赖关系列表替换的依赖关系。

30. **\<mvc:resource>**

    出现一个很奇怪的问题:sweat:，为什么我的html文件调不了css文件呢？bootstrap的css文件可以访问，我自己写的common.css文件就不行？貌似不是，我在servlet容器中定义了<mvc:default-servlet-handler>了，而且bootstrap的css等文件也可以访问了，但是为何就那一个文件访问不了？图片也访问不了

    所以我就只能在在Servlet容器中再设置`<mvc:resource>`这个静态资源直接映射了。这样的话，就需要全部设置我的静态资源了，但是这个`<mvc:resource>`有个好处，就是它可以设置在任何地方。任君设置。

