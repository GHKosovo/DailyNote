没使用过分页之前，不觉得分页难，等到需要用到的时候，完全没有头绪，直到遇到<span class="ljspan ljspan-reverse ljspan-red">Mybatis Pagination</span> - <span class="ljspan ljspan-reverse ljspan-blue">PageHelper</span>才让我松了一口气，<span class="ljspan ljspan-reverse ljspan-blue">PageHelper</span>封装了很多分页的功能，判断前一页/后一页，第一页或最后一页；几个页面的数据封装在列表中，一次只显示这几个页面，快来看看吧:laughing:<!--more-->

### 配置

**引入依赖包**

```java
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>x.x.x</version>
</dependency>
```

**配置PageHelper**

> 使用mybatis-config.xml配置，还可以使用Spring容器配置，详情请看[官方文档](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/en/HowToUse.md)

```
<!-- 
    In the configuration file, 
    plugins location must meet the requirements as the following order:
    properties?, settings?, 
    typeAliases?, typeHandlers?, 
    objectFactory?,objectWrapperFactory?, 
    plugins?, 
    environments?, databaseIdProvider?, mappers?
-->
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- config params as the following -->
        <!--offset 等于 pageNum, limit 等于 pageSize-->
        <property name="RowBounds" value="true"/>
        <!--pageNum <= 0 等于 the first page,  pageNum> pages(total pages)等于 the last page-->
        <property name="reasonable" value="true"/>
        <property name="param1" value="value1"/>
	</plugin>
</plugins>
```

更多关于参数的配置，请参考[官方文档](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/en/HowToUse.md)

<hr>

### 使用

> 我直接使用pageInfo对象来操作分页数据的

```
/获取第1页，10条内容，默认查询总数count
PageHelper.startPage(1, 10);
List<User> list = userMapper.selectAll();
//用PageInfo对结果进行包装
PageInfo page = new PageInfo(list);
//这里的page是一个对象，可以通过引入fastjson包，
//直接解析对象为json对象返回页面
//这样就能一步到位，在前端获取到分页信息。

//测试PageInfo全部属性
//PageInfo包含了非常全面的分页属性
assertEquals(1, page.getPageNum());
assertEquals(10, page.getPageSize());
assertEquals(1, page.getStartRow());
assertEquals(10, page.getEndRow());
assertEquals(183, page.getTotal());
assertEquals(19, page.getPages());
assertEquals(1, page.getFirstPage());
assertEquals(8, page.getLastPage());
assertEquals(true, page.isFirstPage());
assertEquals(false, page.isLastPage());
assertEquals(false, page.isHasPreviousPage());
assertEquals(true, page.isHasNextPage());
```