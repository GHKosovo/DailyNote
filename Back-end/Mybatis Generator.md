# Mybatis Generator

## #前言

通过`mybatis`跟数据库做IO，很方便，但是每次要写的`mapper`和`DAO`包括`Bean`，东西实在是太多了:cry:。所以通过颜群老师的在线课堂，我了解了<span class="ljspan ljspan-reverse ljspan-blue">[Mybatis Generator](http://mybatis.org/generator/index.html)</span>,可以一次性生成`Dao`、`mapper`和与数据库表对应的`bean`:laughing:，真的很爽诶。

<!--more-->

<hr>

## 配置

在<span class="ljspan ljspan-blue">[官方文档](http://mybatis.org/generator/quickstart.html)</span>上，相关配置写得很清楚哦

### 1.导入依赖包

```xml
#引入依赖包
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.5</version>
</dependency> 
```

### 2.编写MBG(mybatis generator)文件

```xml
<!DOCTYPE generatorConfiguration PUBLIC
 "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
 "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--生成Mybatis3Simple形式的配置文件-->
  <context id="simple" targetRuntime="MyBatis3Simple">
    <!--配置数据库连接-->
    <jdbcConnection driverClass="org.hsqldb.jdbcDriver"
        connectionURL="jdbc:hsqldb:mem:aname"
        userId="username"
        password="password" />
	<!--配置数据库表生成的对应bean-->
    <javaModelGenerator targetPackage="example.model" targetProject="src/main/java"/>
	<!--配置mapper-->
    <sqlMapGenerator targetPackage="example.mapper" targetProject="src/main/resources"/>
	<!--配置dao-->
    <javaClientGenerator type="XMLMAPPER" targetPackage="example.mapper" targetProject="src/main/java"/>
	<!--配置数据库表-->
    <table schema="wxproject' tableName="FooTable"  domainObjectName="FooTable" />
  </context>
</generatorConfiguration>
```

>生成的`mybatis`相关文件内容，都跟MBG文件:eyes:有关

<hr>

## 实现

### 运行MBG配置文件

有很[多种方式](http://mybatis.org/generator/running/running.html)，我使用`java`方法，直接调用测试运行或者`main`函数内与运行

```java
  List<String> warnings = new ArrayList<String>();
   boolean overwrite = true;
   //你的mbg文件，可以直接放在项目根目录中，这样就不用写路径了
   File configFile = new File("generatorConfig.xml");
   ConfigurationParser cp = new ConfigurationParser(warnings);
   Configuration config = cp.parseConfiguration(configFile);
   DefaultShellCallback callback = new DefaultShellCallback(overwrite);
   MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
   myBatisGenerator.generate(null);
```

<hr>

## Mybatis Example

刚开始接触到<span class="ljspan ljspan-green">mybatis Example</span>的时候，我都是绕道走（甚至干脆不生成它），自己在mybatis生成的实例里面重新写方法，后面才发现它，大有用处:joy:，一起来看看吧！

```dart
int countByExample(UserExample example) thorws SQLException    按条件计数
int deleteByPrimaryKey(Integer id) thorws SQLException    按主键删除
int deleteByExample(UserExample example) thorws SQLException    按条件查询
String/Integer insert(User record) thorws SQLException    插入数据（返回值为ID）
User selectByPrimaryKey(Integer id) thorws SQLException    按主键查询
ListselectByExample(UserExample example) thorws SQLException    按条件查询
ListselectByExampleWithBLOGs(UserExample example) thorws SQLException    按条件查询（包括BLOB字段）。只有当数据表中的字段类型有为二进制的才会产生。
int updateByPrimaryKey(User record) thorws SQLException    按主键更新
int updateByPrimaryKeySelective(User record) thorws SQLException    按主键更新值不为null的字段
int updateByExample(User record, UserExample example) thorws SQLException    按条件更新
int updateByExampleSelective(User record, UserExample example) thorws SQLException    按条件更新值不为null的字段
```

> 很多关于<span class="ljspan ljspan-green">mybatis Example</span>的方法吧

### 案例：

Mybatis的逆向工程中会生成实例及实例对应的example，example用于添加条件，相当where后面的部分

```
 xxxExample example = new xxxExample();
 Criteria criteria = new Example().createCriteria();
 example.
```

> example和criteria有很多的方法可供使用

```csharp
example.setOrderByClause(“字段名 ASC”);    添加升序排列条件，DESC为降序
example.setDistinct(false)    去除重复，boolean型，true为选择不重复的记录。
criteria.andXxxIsNull    添加字段xxx为null的条件
criteria.andXxxIsNotNull    添加字段xxx不为null的条件
criteria.andXxxEqualTo(value)    添加xxx字段等于value条件
criteria.andXxxNotEqualTo(value)    添加xxx字段不等于value条件
criteria.andXxxGreaterThan(value)    添加xxx字段大于value条件
criteria.andXxxGreaterThanOrEqualTo(value)    添加xxx字段大于等于value条件
criteria.andXxxLessThan(value)    添加xxx字段小于value条件
criteria.andXxxLessThanOrEqualTo(value)    添加xxx字段小于等于value条件
criteria.andXxxIn(List<？>)    添加xxx字段值在List<？>条件
criteria.andXxxNotIn(List<？>)    添加xxx字段值不在List<？>条件
criteria.andXxxLike(“%”+value+”%”)    添加xxx字段值为value的模糊查询条件
criteria.andXxxNotLike(“%”+value+”%”)    添加xxx字段值不为value的模糊查询条件
criteria.andXxxBetween(value1,value2)    添加xxx字段值在value1和value2之间条件
criteria.andXxxNotBetween(value1,value2)    添加xxx字段值不在value1和value2之间条件
```


<hr>


## 附加配置

针对`mybatis`，它还有一个配置文件[MapperConfig.xml](http://mybatis.org/generator/afterRunning.html)，它对<span class="ljspan ljspan-black">[MyBatis](https://mybatis.org/mybatis-3/zh/index.html) </span>行为的设置和属性信息有关，可以通过<span class="ljspan ljspan-reverse ljspan-black">[配置参数](https://mybatis.org/mybatis-3/zh/configuration.html#settings)</span>来设置生成mybatis相关文件的内容形式，比如`mapUnderscoreToCamelCase`开启`驼峰命名自动映射`

<hr>

## 注意事项

1.一般使用`mybatis generator`生成的`bean`,都是没有构造函数的；所以需要自己新建，但是如果有有参构造函数，就必须要有无参构造函数，这是必须的。

2.使用mybatis generator生成mapper.xml(也就是数据库语言)，发现只有几个功能，而且关键的insertSelective方法没有，这就很尴尬了:confounded:，我改了依赖包发现没有用，最后发现是在mbg的配置文件中\<context>中的属性targetRuntime,从原来的MyBatis3Simple改为MyBatis3，就可以了。

3.:boom:记得在`SpringIoC`容器中添加`mapperscannerconfigurer`这个`bean`，不然无法使用`dao`层里面的接口，因为`<context:component-scan>`扫描不包括`dao`层。

<hr>

## 常见问题

#### **1.MyBatis Generator 生成器把其他数据库的同名表生成下来的问题**

这是因为mysql不支持schema和catalog，所以需要在\<jabcConnection>中添加属性“ \<property name="nullCatalogMeansCurrent" value="true"/>”

:dizzy_face:我看不懂:turtle::turtle:，反正加了就对了，既解决了问题，加了对程序也没啥影响。

[参考文档](https://blog.csdn.net/qq_40233736/article/details/83314596)