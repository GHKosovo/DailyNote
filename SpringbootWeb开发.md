## SpringbootWeb开发

### 自定义webmvc

自定义webmvc的配置方法有两种，效果都一样

1.继承webmvcconfigurer重写addInteceptor等的方法

2.使用配置类添加@Bean的方法，返回webmvcconfigurer，在这个webmvcconfigurer中，重写addInterceptor等方法

**addInterceptor方法**：就是用来添加拦截器，addPathPatterns用于添加路径映射，而excludePathPatterns则相反，用于忽略路径映射，忽略的路径映射不会通过拦截器；

**addViewController方法**：如果写一个controller只为了映射一个页面，没有校验什么的，则用这个方法，因为它最方便，简单

### 请求和转发

1.使用ajax时，后台的请求转发和请求重定向不会被执行；

因为ajax的出现就是为了防止发送请求后刷新整个页面的，所以这里的请求转发和请求重定向都不会生效

2.post请求可以被重定向，但是不能被转发； 



## 数据访问

springboot配置spring data jpa十分简单

只需在配置文件中，设置好数据源和jpa设置后

创建dao包，容纳repository接口（需要继承jpaRepository），然后再repository接口中添加CRUD功能的方法了

然后最后的步骤就是创建实体类了,这里的数据需要仔细，因为这关乎数据库表的实现，jps会根据实体类来自行创建数据表

CRUD方法也无需注解和实现，EntityManager也无需创建，springboot都已经帮忙创建好了。

![img](file:///C:/Users/llj/AppData/Roaming/Foxmail7/Temp-12200-20210306125936/Attach/Catch(03-06-22-59-45).jpg)

