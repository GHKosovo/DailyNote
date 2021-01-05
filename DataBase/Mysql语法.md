## 关系型数据库的语法

#### 首先是针对不同操作的区别：

查询：**select** column **from** table **where** condition

更新：**update** table **where** condition **set** column-attribute

删除：**delete from** table **where** condition

增加：**insert into** table **values**(data)

返回唯一不同的的值：distinct

用于规定选择的标准：where

对结果集进行排序：order by (默认是升序（asc）,降序为desc)

把where语句的多个条件结合起来：AND & OR

#### 关键字

- 联结表：join on（可自表联结）
- 分组：group by
- 组内筛选：having (经常跟group by结合在一块，多用于avg()，sum()，count()，功能类似于where)
- 搜寻指定模式：like  (有like ，就有not like)
- 在where 中规定多个值：in (有 in，就有not in)
- 更改字段名：as + 新字段名/空格后+新字段名
- 限制行数：limit+行数
- 选取介于两个值之间的数据范围：between...and(在where中使用)
- 判断语句：case when condition then result1 else result2 end
- Null值：无法与0比较，也无法使用比较运算符，需要用IS NULL 和 IS NOT NULL
- 排除某个字段：<> (在where中使用)

> 以下两个语句是相同的功能
>
> case id when '01' then 1 else null end 
>
> case when id = '01' then 1 else null end 

#### 方法函数

返回数值列的总数（总额）：sum()

返回匹配指定条件的行数：count()

返回数值列的平均值：avg()

返回一列中的最小值：min()

返回一列中的最大值：max()

联结两个字符或字段：concat(a,b)

排名：

- RANK：**并列跳跃排名**，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，跳跃到总共的排名。

- DENSE_RANK：**并列连续排序**，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，依然按照连续数字排名。

- ROW_NUMBER：**连续排名**，即使相同的值，依旧按照连续数字进行排名。

rank() over(order by xx) 以xx为序进行排名

​			dense_rank() over(order by xx)



#### 补充说明

1. where其实可以和having一块在同一个sql语句中使用
2. 查询字段设置别名后，在order by等语句中可以使用这些别名作为属性。