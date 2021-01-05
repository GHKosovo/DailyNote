# Spring注解分析

### @Autowired

:leaves:`Spring`可以实现控制反转，就是实例化对象，但是如果对象里面有属性呢？那就用注解`@autowired `直接注入属性(实例化的对象)，这样就实现了解耦:hear_no_evil:，`Controller、Service、Dao`等分层就是基于此。

- `bean`作用域`singleton`和`prototype`的区别

`singleton`：容器在初始化时，就会创建对象；单例也支持延迟加载（就是只有在请求时再生成）；

`prototype`：容器在初始化时，不创建对象，只是在每次使用时（每次容器获取对象时）再创建对象；并且每次获取对象都会创建一个新的对象；

> `@Resource`---它其实跟`@Autowired`效果一样，只不过它是根据`name`来注入实例化对象的，而Autowired则根据`id`

<hr>

### @ResponseBody

`@responseBody`注解的作用是将`controller`中的方法返回的数据，通过适当的转换器转换为指定的格式之后，写入到`response`对象的`body`区，通常用来返回`JSON`数据或者是`XML`数据:dizzy_face:

需要注意的是，在使用此注解之后不会再走**视图处理器**这个流程，而是直接将数据写入到**输入流**中:grin:，也就是说，只返回数据而不是页面，他的效果等同于通过`response`对象输出指定格式的数据。

<hr>

### 测试三件套:japanese_goblin:

#### *@Test、@ContextConfiguration、@Runwith*

我想使用`@Test`测试来完成注解形式的`SpringIoC`，但是测试无法实现，使用`Java Application`就可以实现！原来测试初始化IoC容器可以用`@ContextConfiguration`这个注解，不管是`XML`形式还是注解形式的IoC容器。

`@ContextConfiguration`和`@Runwith`二者一般不分开，一个是用于导入测试使用的测试库函数，一个用于加载配置文件，也就是加载配置`XML`文件或者`Java文件`（使用`@Configuration`标注配置`java`类文件），用以导入某些`Spring`容器的`bean`对象。

> 详情请看：**/demo2/src/main/java/cn/lj/test/addusertest.java**:feet:

如果需要加载spring容器，可以在`@ContextConfiguration`中加载`Spring`容器后，用于实例化`Spring容器`对象

> 示例：/wx/src/main/java/cn.lj.wx.utils.CreateUtils方法:feet:

<hr>

### @Component

新建一个`Utils`类:guitar:，有个属性`token`和两个方法，在其他类通过`@Autowired`注入这个类对象时，一直报没有这个bean对象，很奇怪，最后把属性token去掉就可以了。原来在引入`bean`对象时，需要配置好他的属性，所以只要属性设置好`Getter/Setter`方法就可以使用`Autowired`自动装配了

最好还是不要设置属性吧:nail_care:，或者直接使用`@Autowired`或`@Resource`注入属性(跟`Controller`、`Service`一样)，就可以直接把这个类设为`component`类，让其他类可以调用。

<hr>

### @Data、@Getter

省略对象get/set方法的编写，省略无参构造函数的编写，怎么做到呢？那当然要使用<span class="ljspan ljspan-blue">[lombok](https://objectcomputing.com/resources/publications/sett/january-2010-reducing-boilerplate-code-with-project-lombok)</span>

#### 配置

```xml
<!-- 注解Get/Set等方法 -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.8</version>
    <scope>provided</scope>
</dependency>
```

#### 下载安装`lombok`的插件

其实就是下载<span class="ljspan ljspan-blue">[lombok.jar](https://projectlombok.org/all-versions)</span>后，打开它，弹出安装界面，然后在打开的界面中指定你的eclipse安装的根目录后安装/更新，最后安装完成之后，请确认eclipse安装路径下是否多了一个lombok.jar包，并且其配置文件eclipse.ini中是否 添加了如下内容:-javaagent:path\to\lombok.jar

具体操作步骤可[参考博主](https://www.cnblogs.com/boonya/p/10691466.html)的安装步骤

<hr>

### Controller中获取输入参数的注解

1. 处理参数最常用的方法：**@ModelAttribute**（spring书上特别介绍的方法）
2. 处理Request的uri的参数：**@PathVariaable**（比较少用）
3. 处理request header的参数：**@RequestHeader**
4. 获取cookie中的值：**@CookieValue**
5. 验证某个模型对象：**@Validated**
6. 处理request body的参数：**@RequestBody**和**@RequestParam**

> @RequestBody一般用来处理请求体中的json数据

​	