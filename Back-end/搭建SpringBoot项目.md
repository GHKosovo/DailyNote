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

