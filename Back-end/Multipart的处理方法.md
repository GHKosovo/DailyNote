[toc]

## #前言

由于之前想要用 <span class="ljspan ljspan-reverse ljspan-blue">apache-common-fileupload</span>来上传文件没有成功，所以这一次有机会借上传微信公众号PDF文件的机会来尝试它。

## 预备依赖包

所需要的包：

[**commons-fileupload**](https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload)<!--more-->

[**commons-io**](https://mvnrepository.com/artifact/commons-io/commons-io)(这个是commons-fileupload的依赖)

然后根据apache官方文档写程序，如下

![image-20200929164203171](C:\Users\llj\Documents\typero图像\image-20200929164203171.png)

我发现fileUpload无法解析request，我觉得很奇怪，为什么呢？

后面发现`web.xml`里面配置了multipart-config

那也就是说，我本身是有解析 <span class="ljspan ljspan-blue">multipart</span>的工具的，查看了一下，发现还不少！:turtle::turtle:

- { org.springframework.web.multipart.commons.CommonsMultipartResolver } 

  for Apache Commons FileUpload

- { org.springframework.web.multipart.support.StandardServletMultipartResolver }

  for the Servlet 3.0+ Part API

>详情请查看这个类{%label warning@org.springframework.web.multipart.MultipartResolver %}，写得很详细。

这下真相大白了，原来我的系统里面spring本身就封装了 <span class="ljspan ljspan-reverse ljspan-blue">apache-common-fileupload</span>，所以我的做法实在是多此一举。

> 当然也不是没办法用，前提是把系统里面对MultipartResolver的解析给关掉就行

官方解释：By default, **Spring features a** ***MultipartResolver*** **that we'll need to disable to use this library.** Otherwise, it'll read the content of the request before it reaches our *Controller.*

由于spring自身已经可以对 <span class="ljspan ljspan-blue">multipart</span>类型进行解析，所以需要关闭掉，但是我找不到关闭的地方，根据某份[官方文档](https://www.baeldung.com/spring-apache-file-upload)，里面详细述说了关于操作的要点和问题的原因，但是针对的是springboot的，龟龟。

所以自然行不通了:cry:

**因为没有实现过这些方法**，所以想要实现一下CommonsMultipartResolver，但是有些东西还是不懂，我想着，如果spring里面封装了commonMultipartResolver，那么就是说，我也可以使用他咯，可是我发现<span class="ljspan ljspan-reverse ljspan-blue">apache-common-fileupload</span>的许多方法和对象都无法用，龟龟:turtle::turtle:，东西都被改了。

比如解析请求parseRequest等都直接解析好了以后封装到request里面，直接可以调用的，就很离谱，我捣鼓了两天，都在瞎搞，真他么心累。:broken_heart:

详细的东西，有位博主写了，在[这里](https://www.cnblogs.com/tengyunhao/p/7670293.html)

<hr>

## 处理Multipart的方法

> 关于StandardServletMultipartResolvert和CommonsMultipartResolver的两种使用方法

### 1.StandardServletMultipartResolvert的用法<!--more-->

需具备条件：servlet3.0+

- 在springmvc容器中配置添加类对象

```
<bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver"></bean>
```

- 在web.xml文件中的`<servlet>`中配置如下：

```
<multipart-config>
    <max-file-size>20848820</max-file-size>
    <max-request-size>418018841</max-request-size>
    <file-size-threshold>1048576</file-size-threshold>
</multipart-config>
```

### 2.CommonsMultipartResolver的用法

需具备的条件：导入两个额外的包：[**commons-fileupload**](https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload)和[**commons-io**](https://mvnrepository.com/artifact/commons-io/commons-io)

```
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<property name="maxUploadSize" value="20000000"></property>
		<property name="defaultEncoding" value="UTF-8"></property>
</bean>
```

### 处理数据

我觉得都一样，只不过解析出来的MultipartHttpServletRequest不一样而已，获取数值的方式都一样的

法一：解析request获取/直接映射到参数上

```
#解析request请求
MultipartHttpServletRequest multipartRequest = (MultipartHttpServletRequest) request;
#这样就可以获取到MultipartFile了
Map<String, MultipartFile> fileMap = multipartRequest.getFileMap();
```

法二：把数据直接封装到模型类中

```
List<MultipartFile> files = newUser.getFiles();

#要提前封装好模型类并实现序列化	
public class NewUser implements Serializable{
	private static final long serialVersionUID = 1L;
	private String username;
	private List<MultipartFile> files;
	
	....get/set method...
}
```

