## 新建项目

方法

- Spring Initializr
- maven

> 如出现依赖/插件错误，那应该是maven配置出错了

我是用的是Jar打包方式，也就是没有`web.xml`文件的打包方式

## 前端页面

使用thymeleaf框架来映射前端页面和静态资源

**页面**直接在`controller`返回`String`可以直接转化为对应的页面

**静态资源**放在static下的，可直接引用，**`/static/img `**  对应 __`/img/**`__

大部分的**前端框架**，比如bootstrap等都可以用webjars来设置，根据webjars的映射规则， **`/webjar`**  对应 **`/META-INF/resources/webjars`** 

> 如果想省略版本号，可以添加依赖包 `webjars-locator`

## 后端逻辑

由于使用了spring-boot-starter的多个依赖包，所以springweb、mybatis等框架都可以直接使用自动配置

但是c3p0这个框架是外部引入的，所以无法使用自动配置的方法配置，所以需要自己写配置类

也就是说，如果starter依赖包有的可以自动配置，没有的需要自己配置！

## 测试

在测试里面，我用Mybatis Generator生成一些mybatis框架所需的内容。配置内容也都放在测试包中，直接生成配置内容。

## 部署

想打包成jar包后直接在cmd中使用jvm直接运行，但是发现，使用**IDEA的打包方式**，一直报错（Unable to start ServletWebServerApplicationContext due to missing ServletWebServerFactory bean.），我也不知道是乍回事

但是在**idea**上运行没问题的；

后面使用**maven打包**后就可以直接在cmd上直接与运行了