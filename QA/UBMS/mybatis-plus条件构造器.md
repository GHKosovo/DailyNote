## EntityWrapper简介

1. Mybatis-Plus通过EntityWrapper（简称**EW**，MP封装的一个查询条件构造器）或者Condition（与EW类似）来让用户自由的构建查询条件，简单便捷，没有额外的负担，能够有效提高开发效率。
2. 实体包装器，主要用于处理SQL拼接、排序、实体参数查询等。
3. **注意：使用的是数据库的字段名，而不是java属性。**
4. 条件参数说明：

| 查询方式     | 说明                         |
| ------------ | ---------------------------- |
| setSqlSelect | 设置SELECT查询字段           |
| where        | where语句，拼接-where条件    |
| and          | AND语句，拼接-AND字段=值     |
| andNew       | AND语句，拼接-AND（字段=值） |
| or           | OR语句，拼接 - OR 字段=值    |
| orNew        | OR语句，拼接 - OR（字段=值） |
| eq           | 等于=                        |
| allEq        | 基于map内容等于=             |
| ne           | 不等于<>                     |
| gt           | 大于>                        |
| ge           | 大于等于>=                   |
| lt           | 小于                         |
| le           | 小于等于<=                   |
| like         | 模糊查询LIKE                 |
| notlike      | 模糊查询NOT LIKE             |
| in           | IN查询                       |
| noin         | NOT IN查询                   |
| inNull       | NULL值查询                   |
| isNotNull    | IS NOT NULL                  |
| groupBy      | 分组GROUP BY                 |
| having       | HAVING关键词                 |
| orderBy      | 排序ORDER BY                 |
| orderAsc     | ASC排序ORDER BY              |
| orderDesc    | DESC排序ORDER BY             |

其实之前也用过Mybatis的Criteria类来设置条件构造器，这里是MybatisPlus，换了个类而已

## 总结

MP：EntityWrapper&&Condition条件构造器。



MyBatis MGB(逆向工程):XxxExample-->Criteria:QBC风格（Query ByCriteria）
 

 Hibernate、通用Mapper也是有QBC风格的。
 MP中实现与QBC较为相似。

[详情请看这里--简书的详解](https://www.jianshu.com/p/f3cd8f850684)

