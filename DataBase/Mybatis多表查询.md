## Mybatis实现多表联合查询

有两种类型（类中包含另一个的分类）：

- 单个对象
- 集合对象

### 单个对象

#### 1.使用\<resultMap>标签在两个表中关联单个对象(N+1方式)

- N+1查询的方式，先查询出一个表的全部信息，根据这个表的信息查询另一个表的信息。
- 实现步骤在Student类中包含一个Teacher对象 
- 在StudentMapper.xml文件中写上查询学生的sql，然后通过\<resultMap>来完成Teacher对象的查询

##### 具体代码

java实体类：

```java
public class Student {
	private int id;
	private String name;
	private int age;
	private int tid;
	private Teacher teacher;
```

TeacherMapper.xml文件中提供一个查询老师对象的sql：

```xml
<mapper namespace="com.test.mapper.TeacherMapper">
	<select id="selById" parameterType="int" resultType="Teacher">
	select * from teacher where id =#{0}
	</select>
</mapper>
```

在StudentMapper.xml中写上查询学生信息：

```xml
<mapper namespace="com.test.mapper.StudentMapper">
	<resultMap type="Student" id="stuMap">
		<id column="id" property="id"/>
		<result column="name" property="name"/>
		<result column="age" property="age"/>
		<result column="tid" property="tid"/>
		<association property="teacher" select="com.test.mapper.TeacherMapper.selById" 
        column="tid"></association>
	</resultMap>
	<select id="selAll" resultMap="stuMap" >
		select * from student
	</select>
</mapper>
```

#### 2.使用\<resultMap>实现关联单个对象（联合查询方式）

只需要写一条SQL,在StudentMapper.xml中完成，对于学生属性直接用<id>或<result>进行装配(将字段别名与属性匹配)，对于Teacher对象直接用<association>标签来映射，其中 property还是代表在类中该对象属性的名称   另外要设置javaType表示返回值类型，其它的还一次对应匹配即可。具体代码实例：

```xml
<!-- 在mapper中实现联合查询 -->
	<resultMap type="Student" id="stuMap1">
		<id column="sid" property="id"/>
		<result column="sname" property="name"/>
		<result column="sage" property="age"/>
		<result column="tid" property="tid"/>
		<association property="teacher" javaType="teacher">
			<id column="tid" property="id"/>
			<result column="tname" property="name"/>
		</association>
	</resultMap>
	
	<select id="selAll1" resultMap="stuMap1">
	select s.id sid,s.name sname,s.age sage,t.id tid,t.name tname 
	from student s left join teacher t on s.tid = t.id;
	</select>
```



### 多个对象

#### 使用\<resultMap>查询关联集合对象（N+1）

在实体类Teacher中加上含有多个Student的属性list，代码：

```java
public class Teacher {
	private int id;
	private String name;
	private List<Student> list;
```

在StudentMapper.xml中添加通过tid查询学生的sql语句,代码：

```xml
<mapper namespace="com.test.mapper.StudentMapper">
	<select id="selByTid" resultType="student" parameterType="int" >
		select * from student where tid = #{0}
	</select>
</mapper>
```

在TeacherMapper.xml中添加查询全部，然后通过\<resultMap>来装配其它的，代码：

```xml
<mapper namespace="com.test.mapper.TeacherMapper">
    <resultMap type="Teacher" id="teacMap">
	    <id column="id" property="id"/>
	    <result column="name" property="name"/>
	    <collection property="list" select="com.test.mapper.StudentMapper.selByTid" column="id"/>
    </resultMap>
    <select id="selAll" resultMap="teacMap">
	    select * from teacher
    </select>
</mapper>

```

还是和查询单个对象一样这里\<resultMap>中使用\<collection>来匹配集合，其中property还是类中的属性名，select是要执行的sql语句，column为要传递的参数字段。

#### 使用\<resultMap>实现加载集合数据（联合查询方式）

只需要写一条SQL,在TeacherMapper.xml中完成，对于老师的属性在<resultMap>中直接用<id>或<result>进行装配(将字段别名与属性匹配)，对于Student对象集合用<collection>标签来映射，其中 property还是代表在类中该对象集合属性的名称   另外要设置ofType表示返回集合的泛型，其它的还一次对应匹配即可。具体代码实例：


```xml
<!-- 使用联合查询 -->
	<resultMap type="Teacher" id="teacMap1">
		<id column="tid" property="id"/>
		<result column="tname" property="name"/>
		<collection property="list" ofType="Student">
			<id column="sid" property="id"/>
			<result column="sname" property="name"/>
			<result column="age" property="age"/>
			<result column="tid1" property="tid"/>
		</collection>
	</resultMap>
	<select id="selAll1" resultMap="teacMap1">
		select t.id tid, t.name tname, s.id sid, s.name sname,age ,s.tid tid1
		from teacher t left join student s on t.id = s.tid
	</select>
```

