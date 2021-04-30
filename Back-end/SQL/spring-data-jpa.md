关于jpa的简单使用方法，请看 [JPA](jpa.md) 这一章节

# 一、JPA的三种常见用法：

- 一对一
- 一对多/多对一
- 多对多

## 一对一

#### 数据库关系表

![An ER Diagram mapping Users to Addresses via an address_id foreign key](https://www.baeldung.com/wp-content/uploads/2018/12/1-1_FK.png)

#### 实体类

对于拥有外键的一方：

```java
@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //... 

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id", referencedColumnName = "id")
    private Address address;

    // ... getters and setters
}

## cascade:级联操作 
```

而对于拥有主键的一方,也即是是被拥有方

```java
@Entity
@Table(name = "address")
public class Address {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //...

    @OneToOne(mappedBy = "address")
    private User user;

    //... getters and setters
}

## mappedby:关联的表名
```

这里可以看到只需要添加getters and setters方法，如果还需要添加toString方法，则添加只可以添加一方而已，不然会出现不断嵌套获取的情况（比如用户中地址，地址中又有用户，用户中又有地址，会报栈溢出的错误）

[参考链接](https://www.baeldung.com/jpa-one-to-one)



再此之前，对于一对多和多对一的懒加载问题一直没有解决，不只是何原因，只能被迫使用"FetchType.EAGER"

## 一对多/多对一

#### 数据库关系表

![img](https://www.baeldung.com/wp-content/uploads/2018/11/12345789.png)

#### 实体化类

> 拥有外键的一方，即拥有放

#Email

```java
@Entity
public class Email {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "employee_id")
    private Employee employee;

    // ...

}
```

#Employee

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "employee")
    private List<Email> emails;
    
    // ...
}
```

[参考链接](https://www.baeldung.com/jpa-joincolumn-vs-mappedby#:~:text=The%20%40JoinColumn%20annotation%20helps%20us%20specify%20the%20column,the%20difference%20between%20%40JoinColumn%20and%20mappedBy%20in%20JPA.)



## Many to Many

#### 数据库关系表

![img](https://www.baeldung.com/wp-content/uploads/2018/11/simple-model-updated.png)

#### 实体化类

```java
@Entity
class Student {

    @Id
    Long id;

    @ManyToMany
	@JoinTable(
  		name = "course_like", 
  		joinColumns = @JoinColumn(name = "student_id"), 
  		inverseJoinColumns = @JoinColumn(name = "course_id"))
    Set<Course> likedCourses;

    // additional properties
    // standard constructors, getters, and setters
}

##@JoinTable是加入第三张表，name为表名，joinColums为拥有方的关联列，而inverseJoinColums就是相对应的被拥有方的关联列


@Entity
class Course {

    @Id
    Long id;

    @ManyToMany(mappedBy = "likedCourses")
    Set<Student> likes;

    // additional properties
    // standard constructors, getters, and setters
}
```

[参考链接](https://www.baeldung.com/jpa-many-to-many)