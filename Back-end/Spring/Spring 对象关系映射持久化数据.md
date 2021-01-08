**对象--关系映射**就是大家所说的ORM

这项技术具体功能就是将对象的属性映射到数据库的列上，并且自动生成语句和查询。这样我们就能从无休止的问号字符串中解脱出来。<!--more-->

此外，我们还需要一些更复杂的特性： 

- 延迟加载（Lazy loading）：随着我们的对象关系变得越来越复杂，有时候我们并不希望立即获取完整的对象间关系。举一个典型的例子，假设我们在查询一组PurchaseOrder对象，而每个对象中都包含一个LineItem对象集合。如果我们只关心PurchaseOrder的属性，那查询出LineItem的数据就毫无意义。而且这可能是开销很大的操作。延迟加载允许我们只在需要的时候获取数据。 
- 预先抓取（Eager fetching）：这与延迟加载是相对的。借助于预先抓取，我们可以使用一个查询获取完整的关联对象。如果我们需要PurchaseOrder及其关联的LineItem对象，预先抓取的功能可以在一个操作中将它们全部从数据库中取出来，节省了多次查询的成本。 
- 级联（ Cascading）：有时，更改数据库中的表会同时修改其他表。回到我们订购单的例子中，当删除Order对象时，我们希望同时在数据库中删除关联的LineItem 。 

一些可用的框架提供了这样的服务，这些服务的通用名称是对象/关系映射（object-relational mapping，ORM）。在持久层使用ORM工具，可以节省数千行的代码和大量的开发时间。ORM工具能够把你的注意力从容易出错的SQL代码转向如何实现应用程序的真正需求。

Spring对多个持久化框架都提供了支持，包括Hibernate、iBATIS、Java数据对象（Java Data Objects，JDO）以及Java持久化API（Java Persistence API，JPA）。与Spring对JDBC的支持那样，Spring对ORM框架的支持提供了与这些框架的集成点以及一些附加的服务： 

- 支持集成Spring声明式事务； 
- 透明的异常处理； 
- 线程安全的、轻量级的模板类； 
- DAO支持类； 
- 资源管理。 

本章没有足够的篇幅介绍Spring支持的全部ORM框架。其实这并不会有什么问题，因为Spring对不同ORM解决方案的支持是很相似的。一旦掌握了Spring对某种ORM框架的支持后，你可以轻松地切换到另一个框架。

## 在Spring中集成Hibernate

Hibernate是在开发者社区很流行的开源持久化框架。它不仅提供了基本的对象关系映射，还提供了ORM工具所应具有的所有复杂功能，比如缓存、延迟加载、预先抓取以及分布式缓存。

使用Hibernate所需的主要接口是org.hibernate.Session 。 Session接口提供了基本的数据访问功能，如保存、更新、删除以及从数据库加载对象的功能。通过Hibernate的Session接口，应用程序的Repository能够满足所有的持久化需求。

获取Hibernate Session对象的标准方式是借助于Hibernate SessionFactory接口的实现类。除了一些其他的任务，SessionFactory主要负责Hibernate Session的打开、关闭以及管理。

在Spring中，我们要通过Spring的某一个Hibernate Session工厂bean来获取Hibernate SessionFactory。从3.1版本开始，Spring提供了三个Session工厂bean供我们选择： 

1. org.springframework.orm.hibernate3.LocalSessionFactoryBean 
2. org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean 
3. org.springframework.orm.hibernate4.LocalSessionFactoryBean 

这些Session工厂bean都是Spring FactoryBean接口的实现，它们会产生一个HibernateSessionFactory，它能够装配进任何SessionFactory类型的属性中。这样的话，就能在应用的Spring上下文中，与其他的bean一起配置Hibernate Session工厂。

至于选择使用哪一个Session工厂，这取决于使用哪个版本的Hibernate以及你使用XML还是使用注解来定义对象-数据库之间的映射关系。如果你使用Hibernate 3.2或更高版本（直到Hibernate 4.0，但不包含这个版本）并且使用XML定义映射的话，那么你需要定义Spring的org.springframework.orm.hibernate3 包中的 LocalSessionFactoryBean ：

```java
@Bean
public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
  LocalSessionFactoryBean sfb = new LocalSessionFactoryBean();
  sfb.setDataSource(dataSource);
  sfb.setMappingResources(new String[] { "Spitter.hbm.xml" });
  Properties props = new Properties();
  props.setProperty("dialect", "org.hibernate.dialect.H2Dialect");
  sfb.setHibernateProperties(props);
  return sfb;
}
```

在配置 LocalSessionFactoryBean时，我们使用了三个属性。属性dataSource装配了一个DataSource bean的引用。属性mappingResources列出了一个或多个的**Hibernate映射文件**，在这些文件中定义了应用程序的持久化策略。最后，hibernateProperties属性配置了Hibernate如何进行操作的细节。在本示例中，我们配置Hibernate使用H2数据库并且要按照H2Dialect来构建SQL。

> 这里的Hibernate映射文件其实就是那些用于持久化的对象

如果你更倾向于使用注解的方式来定义持久化，并且你还没有使用Hibernate 4的话，那么需要使用AnnotationSessionFactoryBean 来代替 LocalSessionFactoryBean ：

```java
@Bean
public AnnotationSessionFactoryBean sessionFactory(DataSource ds) {
  AnnotationSessionFactoryBean sfb = new AnnotationSessionFactoryBean();
  sfb.setDataSource(ds);
  sfb.setPackagesToScan(new String[] { "com.habuma.spittr.domain" });
  Properties props = new Properties();
  props.setProperty("dialect", "org.hibernate.dialect.H2Dialect");
  sfb.setHibernateProperties(props);
  return sfb;
}
```

如果你使用Hibernate 4的话，那么就应该使用org.springframework.orm.hibernate4 中的 LocalSessionFactoryBean。尽管它与Hibernate 3包中的LocalSessionFactoryBean使用了相同的名称，但是Spring 3.1新引入的这个Session工厂类似于Hibernate 3中LocalSessionFactoryBean 和 AnnotationSessionFactoryBean的结合体。它有很多相同的属性，能够支持基于XML的映射和基于注解的映射。如下的代码展现了如何对它进行配置，使其支持基于注解的映射：

```java
@Bean
public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
  LocalSessionFactoryBean sfb = new LocalSessionFactoryBean();
  sfb.setDataSource(dataSource);
  sfb.setPackagesToScan(new String[] { "com.habuma.spittr.domain" });
  Properties props = new Properties();
  props.setProperty("dialect", "org.hibernate.dialect.H2Dialect");
  sfb.setHibernateProperties(props);
  return sfb;
}
```

在这两个配置中，dataSource 和 hibernateProperties属性都声明了从哪里获取数据库连接以及要使用哪一种数据库。这里不再列出Hibernate配置文件，而是使用**packagesToScan**属性告诉Spring扫描一个或多个包以查找域类，这些类通过注解的方式表明要使用Hibernate进行持久化，这些类可以使用的注解包括JPA的@Entity 或 @MappedSuperclass以及Hibernate的@Entity 。

在Spring应用上下文中配置完Hibernate的Session工厂bean后，那我们就可以创建自己的Repository类了。

#### 构建不依赖于Spring的Hibernate代码

在Spring和Hibernate的早期岁月中，编写Repository类将会涉及到使用Spring的HibernateTemplate 。 HibernateTemplate能够保证每个事务使用同一个Session。但是这种方式的弊端在于我们的Repository实现会直接与Spring耦合。 

现在的最佳实践是不再使用HibernateTemplate，而是使用上下文Session（Contextual session）。通过这种方式，会直接将Hibernate SessionFactory装配到Repository中，并使用它来获取Session，如下面的程序清单所示。

```java
package spittr.db.hibernate4;

import java.io.Serializable;
import java.util.List;

import javax.inject.Inject;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.criterion.Restrictions;
import org.springframework.stereotype.Repository;

import spittr.db.SpitterRepository;
import spittr.domain.Spitter;

@Repository
public class HibernateSpitterRepository implements SpitterRepository {

	private SessionFactory sessionFactory;

	@Inject
	public HibernateSpitterRepository(SessionFactory sessionFactory) {
		this.sessionFactory = sessionFactory;		 //<co id="co_InjectSessionFactory"/>
	}
	
	private Session currentSession() {
		return sessionFactory.getCurrentSession();//<co id="co_RetrieveCurrentSession"/>
	}
	
	public long count() {
		return findAll().size();
	}

	public Spitter save(Spitter spitter) {
		Serializable id = currentSession().save(spitter);  //<co id="co_UseCurrentSession"/>
		return new Spitter((Long) id, 
				spitter.getUsername(), 
				spitter.getPassword(), 
				spitter.getFullName(), 
				spitter.getEmail(), 
				spitter.isUpdateByEmail());
	}

	public Spitter findOne(long id) {
		return (Spitter) currentSession().get(Spitter.class, id); 
	}

	public Spitter findByUsername(String username) {		
		return (Spitter) currentSession() 
				.createCriteria(Spitter.class) 
				.add(Restrictions.eq("username", username))
				.list().get(0);
	}

	public List<Spitter> findAll() {
		return (List<Spitter>) currentSession() 
				.createCriteria(Spitter.class).list(); 
	}
	
}
```

首先，我们通过@Inject注解让Spring自动将一个SessionFactory 注入到 HibernateSpitterRepository 的 sessionFactory属性中。接下来，在currentSession()方法中，我们使用这个SessionFactory来获取当前事务的Session。

另外需要注意的是，我们在类上使用了@Repository注解，这会为我们做两件事情。首先，@Repository是Spring的另一种构造性注解，它能够像其他注解一样被Spring的组件扫描所扫描到。这样就不必明确声明HibernateSpitterRepository bean了，只要这个Repository类在组件扫描所涵盖的包中即可。 

除了帮助减少显式配置以外，@Repository还有另外一个用处。让我们回想一下模板类，它有一项任务就是捕获平台相关的异常，然后使用Spring统一非检查型异常的形式重新抛出。如果我们使用Hibernate上下文Session而不是Hibernate模板的话，那异常转换会怎么处理呢？ 

为了给不使用模板的Hibernate Repository添加异常转换功能，我们只需在Spring应用上下文中添加一个PersistenceExceptionTranslationPostProcessor bean：

```java
@Bean
public BeanPostProcessor persistenceTranslation() {
  return new PersistenceExceptionTranslationPostProcessor();
}
```

PersistenceExceptionTranslationPostProcessor是一个bean后置处理器（bean post-processor），它会在所有拥有@Repository注解的类上添加一个通知器（advisor），这样就会捕获任何平台相关的异常并以Spring非检查型数据访问异常的形式重新抛出。

现在，Hibernate版本的Repository已经完成了。我们开发时，没有依赖Spring的特定类（除了@Repository注解以外）。这种不使用模板的方式也适用于开发纯粹的基于JPA的Repository，让我们再尝试开发另一个SpitterRepository实现类，这次我们使用的是JPA。

## Spring 与Java持久化API

Java持久化API（Java Persistence API，JPA）诞生在EJB 2实体Bean的废墟之上，并成为下一代Java持久化标准。JPA是基于POJO的持久化机制，它从Hibernate和Java数据对象（Java Data Object，JDO）上借鉴了很多理念并加入了Java 5注解的特性。

在Spring中使用JPA的第一步是要在Spring应用上下文中将实体管理器工厂（entity manager factory）按照bean的形式来进行配置。

简单来讲，基于JPA的应用程序需要使用EntityManagerFactory的实现类来获取EntityManager实例。JPA定义了两种类型的实体管理器： 

- 应用程序管理类型（Application-managed）：当应用程序向实体管理器工厂直接请求实体管理器时，工厂会创建一个实体管理器。在这种模式下，程序要负责打开或关闭实体管理器并在事务中对其进行控制。这种方式的实体管理器适合于不运行在Java EE容器中的独立应用程序。 
- 容器管理类型（Container-managed）：实体管理器由Java EE创建和管理。应用程序根本不与实体管理器工厂打交道。相反，实体管理器直接通过注入或JNDI来获取。容器负责配置实体管理器工厂。这种类型的实体管理器最适用于Java EE容器，在这种情况下会希望在persistence.xml指定的JPA配置之外保持一些自己对JPA的控制。

以上的两种实体管理器实现了同一个EntityManager接口。关键的区别不在于EntityManager本身，而是在于EntityManager的创建和管理方式。应用程序管理类型的EntityManager 是由 EntityManagerFactory创建的，而后者是通过PersistenceProvider 的 createEntityManagerFactory()方法得到的。与此相对，容器管理类型的Entity ManagerFactory 是通过 PersistenceProvider 的 createContainerEntityManager Factory()方法获得的。

以上的两种实体管理器实现了同一个EntityManager接口。关键的区别不在于EntityManager本身，而是在于EntityManager的创建和管理方式。应用程序管理类型的EntityManager 是由 EntityManagerFactory创建的，而后者是通过PersistenceProvider 的 createEntityManagerFactory()方法得到的。与此相对，容器管理类型的Entity ManagerFactory 是通过 PersistenceProvider 的 createContainerEntityManager Factory()方法获得的。

这对想使用JPA的Spring开发者来说又意味着什么呢？其实这并没太大的关系。不管你希望使用哪种EntityManagerFactory，Spring都会负责管理EntityManager。如果你使用的是应用程序管理类型的实体管理器，Spring承担了应用程序的角色并以透明的方式处理EntityManager。在容器管理的场景下，Spring会担当容器的角色。

这两种实体管理器工厂分别由对应的Spring工厂Bean创建：

- LocalEntityManagerFactoryBean生成应用程序管理类型的EntityManager-Factory

- LocalContainerEntityManagerFactoryBean生成容器管理类型的Entity-ManagerFactory 。

需要说明的是，选择应用程序管理类型的还是容器管理类型的EntityManager Factory，对于基于Spring的应用程序来讲是完全透明的。当组合使用Spring和JPA时，处理EntityManagerFactory的复杂细节被隐藏了起来，数据访问代码只需关注它们的真正目标即可，也就是数据访问。

##### 配置应用程序管理类型的JPA

对于应用程序管理类型的实体管理器工厂来说，它绝大部分配置信息来源于一个名为persistence.xml的配置文件。这个文件必须位于类路径下的META-INF目录下。

persistence.xml的作用在于定义一个或多个持久化单元。持久化单元是同一个数据源下的一个或多个持久化类。简单来讲，persistence.xml列出了一个或多个的持久化类以及一些其他的配置如数据源和基于XML的配置文件。如下是一个典型的persistence.xml文件，它是用于Spittr应用程序的：

```xml
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
      version="1.0">
    <persistence-unit name="spitterPU">
      <class>com.habuma.spittr.domain.Spitter</class>
      <class>com.habuma.spittr.domain.Spittle</class>
      <properties>
        <property name="toplink.jdbc.driver"
            value="org.hsqldb.jdbcDriver" />
        <property name="toplink.jdbc.url" value=
            "jdbc:hsqldb:hsql://localhost/spitter/spitter" />
        <property name="toplink.jdbc.user"
            value="sa" />
        <property name="toplink.jdbc.password"
            value="" />
      </properties>
    </persistence-unit>
  </persistence>
```

因为在persistence.xml文件中包含了大量的配置信息，所以在Spring中需要配置的就很少了。可以通过以下的@Bean注解方法在Spring中声明LocalEntityManagerFactoryBean ：

```java
@Bean
public LocalEntityManagerFactoryBean entityManagerFactoryBean() {
  LocalEntityManagerFactoryBean emfb
      = new LocalEntityManagerFactoryBean();
  emfb.setPersistenceUnitName("spitterPU");
  return emfb;
}
```

创建应用程序管理类型的EntityManagerFactory都是在persistence.xml中进行的，而这正是应用程序管理的本意。在应用程序管理的场景下（不考虑Spring时），完全由应用程序本身来负责获取EntityManagerFactory，这是通过JPA实现的PersistenceProvider做到的。如果每次请求EntityManagerFactory时都需要定义持久化单元，那代码将会迅速膨胀。通过将其配置在persistence.xml中，JPA就能够在这个特定的位置查找持久化单元定义了。 

但借助于Spring对JPA的支持，我们不再需要直接处理PersistenceProvider了。因此，再将配置信息放在persistence.xml中就显得不那么明智了。实际上，这样做妨碍了我们在Spring中配置EntityManagerFactory（如果不是这样的话，我们可以提供一个Spring配置的数据源）。

##### 配置容器管理类型的JPA

容器管理的JPA采取了一个不同的方式。当运行在容器中时，可以使用容器（在我们的场景下是Spring）提供的信息来生成EntityManagerFactory

你可以将数据源信息配置在Spring应用上下文中，而不是在persistence.xml中了。例如，如下的@Bean注解方法声明了在Spring中如何使用LocalContainerEntity-ManagerFactoryBean来配置容器管理类型的JPA：

```java
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory(
        DataSource dataSource, JpaVendorAdapter jpaVendorAdapter) {
  LocalContainerEntityManagerFactoryBean emfb =
      new LocalContainerEntityManagerFactoryBean();
  emfb.setDataSource(dataSource);
  emfb.setJpaVendorAdapter(jpaVendorAdapter);
  emfb.setPackagesToScan("com.habuma.spittr.domain");
  return emfb;
}
```

这里，我们使用了Spring配置的数据源来设置dataSource属性。任何javax.sql.DataSource的实现都是可以的。尽管数据源还可以在persistence.xml中进行配置，但是这个属性指定的数据源具有更高的优先级。 persistence.xml文件的主要作用就在于识别持久化单元中的实体类。但是从Spring 3.1开始，我们能够在LocalContainerEntityManagerFactoryBean中直接设置packagesToScan 属性

jpaVendorAdapter属性用于指明所使用的是哪一个厂商的JPA实现。Spring提供了多个JPA厂商适配器： 

- EclipseLinkJpaVendorAdapter 
- HibernateJpaVendorAdapter
- OpenJpaVendorAdapter 
- TopLinkJpaVendorAdapter（在Spring 3.1版本中，已经将其废弃了）

在本例中，我们使用Hibernate作为JPA实现，所以将其配置为Hibernate-JpaVendorAdapter ：

```java
@Bean
public JpaVendorAdapter jpaVendorAdapter() {
  HibernateJpaVendorAdapter adapter = new HibernateJpaVendorAdapter();
  adapter.setDatabase("HSQL");
  adapter.setShowSql(true);
  adapter.setGenerateDdl(false);
  adapter.setDatabasePlatform("org.hibernate.dialect.HSQLDialect");
  return adapter;
}
```

有多个属性需要设置到厂商适配器上，但是最重要的是database属性，在上面我们设置了要使用的数据库是Hypersonic。这个属性支持的其他值如表11.1所示。

> 表11.1　Hibernate的JPA适配器支持多种数据库，可以通过其database属性配置使用哪个数据库
>

nihao 

| 数据库平台    | 属性database的值    |
| ------------- | ------------------- |
| IBM DB2       | DB2                 |
| Apache Derby  | DERBY               |
| H2            | H2                  |
| Hypersonic    | HSQL                |
| Informix      | INFORMIX            |
| MySQL         | MYSQL               |
| Oracle        | ORACLE              |
| PostgresQL    | POSTGRESQL          |
| Microsoft SQL | Server    SQLSERVER |
| Sybase        | SYBASE              |

#### 从JNDI获取实体管理器工厂

还有一件需要注意的事项，如果将Spring应用程序部署在应用服务器中，EntityManagerFactory可能已经创建好了并且位于JNDI中等待查询使用。在这种情况下，可以使用Spring jee命名空间下的\<jee:jndi-lookup>元素来获取对EntityManagerFactory 的引用：

```xml
<jee:jndi-lookup id="emf" jndi-name="persistence/spitterPU" />
```

我们也可以使用如下的Java配置来获取EntityManagerFactory ：

```java
@Bean
public JndiObjectFactoryBean entityManagerFactory() {}
JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
  jndiObjectFB.setJndiName("jdbc/SpittrDS");
  return jndiObjectFB;
}
```

尽管这种方法没有返回EntityManagerFactory，但是它的结果就是一个EntityManagerFactory bean。这是因为它所返回的JndiObjectFactoryBean 是 FactoryBean接口的实现，它能够创建EntityManagerFactory 。

### 编写基于JPA的Repository

正如Spring对其他持久化方案的集成一样，Spring对JPA集成也提供了JpaTemplate模板以及对应的支持类JpaDaoSupport。但是，为了实现更纯粹的JPA方式，基于模板的JPA已经被弃用了。这与我们在11.1.2小节使用的Hibernate上下文Session是很类似的。 

鉴于纯粹的JPA方式远胜于基于模板的JPA，所以在本节中我们将会重点关注如何构建不依赖Spring的JPA Repository。如下程序清单中的JpaSpitterRepository展现了如何开发不使用Spring JpaTemplate 的JPA Repository。

```java
package com.habuma.spittr.persistence;
  import java.util.List;
  import javax.persistence.EntityManagerFactory;
  import javax.persistence.PersistenceUnit;
  import org.springframework.dao.DataAccessException;
  import org.springframework.stereotype.Repository;
  import org.springframework.transaction.annotation.Transactional;
  import com.habuma.spittr.domain.Spitter;
  import com.habuma.spittr.domain.Spittle;

  @Repository
  @Transactional
  public class JpaSpitterRepository implements SpitterRepository {

    @PersistenceUnit
    private EntityManagerFactory emf;	

    public void addSpitter(Spitter spitter) {
      emf.createEntityManager().persist(spitter);	
    }

    public Spitter getSpitterById(long id) {
      return emf.createEntityManager().find(Spitter.class, id);
    }

    public void saveSpitter(Spitter spitter) {
      emf.createEntityManager().merge(spitter);
    }
  ...
  }
```

需要注意的是EntityManagerFactory属性，它使用了@PersistenceUnit注解，因此，Spring会将EntityManagerFactory 注入到 Repository之中。有了EntityManagerFactory 之后， JpaSpitterRepository的方法就能使用它来创建EntityManager 了，然后 EntityManager可以针对数据库执行操作。

JpaSpitterRepository中，唯一的问题在于每个方法都会调用createEntityManager()。除了引入易出错的重复代码以外，这还意味着每次调用Repository的方法时，都会创建一个新的EntityManager。这种复杂性源于事务。如果我们能够预先准备好EntityManager，那会不会更加方便呢？

这里的问题在于EntityManager并不是线程安全的，一般来讲并不适合注入到像Repository这样共享的单例bean中。但是，这并不意味着我们没有办法要求注入EntityManager。如下的程序清单展现了如何借助@PersistentContext 注解为 JpaSpitterRepository 设置 EntityManager

```java
package com.habuma.spittr.persistence;
import java.util.List;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import org.springframework.dao.DataAccessException;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import com.habuma.spittr.domain.Spitter;
import com.habuma.spittr.domain.Spittle;

@Repository
@Transactional
public class JpaSpitterRepository implements SpitterRepository {

  @PersistenceContext
  private EntityManager em;	

  public void addSpitter(Spitter spitter) {
    em.persist(spitter);	
  }

  public Spitter getSpitterById(long id) {
    return em.find(Spitter.class, id);
  }

  public void saveSpitter(Spitter spitter) {
    em.merge(spitter);
  }
...
}
```

在这个新版本的JpaSpitterRepository中，直接为其设置了EntityManager，这样的话，在每个方法中就没有必要再通过EntityManagerFactory 创建 EntityManager了。尽管这种方式非常便利，但是你可能会担心注入的EntityManager会有线程安全性的问题。 

这里的真相是@PersistenceContext并不会真正注入EntityManager——至少，精确来讲不是这样的。它没有将真正的EntityManager设置给Repository，而是给了它一个EntityManager的代理。真正的EntityManager是与当前事务相关联的那一个，如果不存在这样的EntityManager的话，就会创建一个新的。这样的话，我们就能始终以线程安全的方式使用实体管理器。

另外，还需要了解@PersistenceUnit 和 @PersistenceContext并不是Spring的注解，它们是由JPA规范提供的。为了让Spring理解这些注解，并注入EntityManager Factory 或 EntityManager，我们必须要配置Spring的Persistence-AnnotationBeanPostProcessor。如果你已经使用了\<context:annotation-config> 或 \<context:component-scan>，那么你就不必再担心了，因为这些配置元素会自动注册PersistenceAnnotationBeanPostProcessor bean。否则的话，我们需要显式地注册这个bean：

```java
@Bean
public PersistenceAnnotationBeanPostProcessor paPostProcessor() {
  return new PersistenceAnnotationBeanPostProcessor();
}
```

你可能也注意到了JpaSpitterRepository 使用了 @Repository 和 @Transactional 注解。 @Transactional表明这个Repository中的持久化方法是在事务上下文中执行的。

同样的，为了让JPA把异常转换成Spring的统一数据访问异常。需要将PersistenceExceptionTranslationPostProcessor装配到Spring中，就像在Hibernate样例中一样

```java
@Bean
public BeanPostProcessor persistenceTranslation() {
  return new PersistenceExceptionTranslationPostProcessor();
}
```

提醒一下，不管对于JPA还是Hibernate，异常转换都不是强制要求的。如果你希望在Repository中抛出特定的JPA或Hibernate异常，只需将PersistenceException-TranslationPostProcessor省略掉即可，这样原来的异常就会正常地处理。但是，如果使用了Spring的异常转换，你会将所有的数据访问异常置于Spring的体系之下，这样以后切换持久化机制的话会更容易。

## 借助Spring Data实现自动化的 JPA Repository

重新审视addSpitter()方法

```java
public void addSpitter(Spitter spitter) {
    entityManager.persist(spitter);
  }
```

在任何具有一定规模的应用中，你可能会以几乎完全相同的方式多次编写这种方法。实际上，除了所持久化的Spitter对象不同以外，我敢打赌你以前肯定写过类似的方法。其实，JpaSpitterRepository中的其他方法也没有什么太大的创造性。领域对象会有所不同，但是所有Repository中的方法都是很通用的。

为什么我们需要一遍遍地编写相同的持久化方法呢，难道仅仅是因为要处理的领域类型不同吗？Spring Data JPA能够终结这种样板式的愚蠢行为。我们不再需要一遍遍地编写相同的Repository实现，Spring Data能够让我们只编写Repository接口就可以了。根本就不再需要实现类了。

```java
public interface SpitterRepository
         extends JpaRepository<Spitter, Long> {
}
```

编写Spring Data JPA Repository的关键在于要从一组接口中挑选一个进行扩展。这里，SpitterRepository扩展了Spring Data JPA的JpaRepository（稍后，我会介绍几个其他的接口）。通过这种方式，JpaRepository进行了参数化，所以它就能知道这是一个用来持久化Spitter对象的Repository，并且Spitter的ID类型为Long。另外，它还会继承18个执行持久化操作的通用方法，如保存Spitter 、删除 Spitter以及根据ID查询Spitter 。

此时，你可能会想下一步就该编写一个类实现SpitterRepository和它的18个方法了。如果真的是这样的话，那本章就会变得乏味无聊了。其实，我们根本不需要编写SpitterRepository的任何实现类，相反，我们让Spring Data来为我们做这件事请。我们所需要做的就是对它提出要求。

为了要求Spring Data创建SpitterRepository的实现，我们需要在Spring配置中添加一个元素。如下的程序清单展现了在XML配置中启用Spring Data JPA所需要添加的内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:jpa="http://www.springframework.org/schema/data/jpa"
  xsi:schemaLocation="http://www.springframework.org/schema/data/jpa
    http://www.springframework.org/schema/data/jpa/spring-jpa-1.0.xsd">

    <jpa:repositories base-package="com.habuma.spittr.db" />

  ...

</beans>
```

\<jpa:repositories>会扫描它的基础包来查找扩展自Spring Data JPA Repository接口的所有接口。如果发现了扩展自Repository的接口，它会自动生成（在应用启动的时候）这个接口的实现。

如果要使用Java配置的话，那就不需要使用\<jpa:repositories>元素了，而是要在Java配置类上添加@EnableJpaRepositories注解。如下就是一个Java配置类，它使用了@EnableJpaRepositories注解，并且会扫描com.habuma.spittr.db 包：

```java
@Configuration
@EnableJpaRepositories(basePackages="com.habuma.spittr.db")
public class JpaConfiguration {
  ...
}
```

让我们回到SpitterRepository接口，它扩展自JpaRepository ，而 JpaRepository又扩展自Repository标记接口（虽然是间接的）。因此，SpitterRepository就传递性地扩展了Repository接口，也就是Repository扫描时所要查找的接口。当Spring Data找到它后，就会创建SpitterRepository的实现类，其中包含了继承自JpaRepository 、 PagingAndSortingRepository 和 CrudRepository的18个方法。 

很重要的一点在于Repository的实现类是在应用启动的时候生成的，也就是Spring的应用上下文创建的时候。它并不是在构建时通过代码生成技术产生的，也不是接口方法调用时才创建的。

Spring Data JPA很棒的一点在于它能为Spitter对象提供18个便利的方法来进行通用的JPA操作，而无需你编写任何持久化代码。但是，如果你的需求超过了它所提供的这18个方法的话，该怎么办呢？幸好，Spring Data JPA提供了几种方式来为Repository添加自定义的方法。让我们看一下如何为Spring Data JPA编写自定义的查询方法。

#### 定义查询方法

现在， SpitterRepository需要完成的一项功能是根据给定的username查找Spitter对象。比如，我们将SpitterRepository接口修改为如下所示的样子：

```java
public interface SpitterRepository
       extends JpaRepository<Spitter, Long> {
    Spitter findByUsername(String username);
  }
```

实际上，我们并不需要实现findByUsername()。方法签名已经告诉Spring Data JPA足够的信息来创建这个方法的实现了。 

当创建Repository实现的时候，Spring Data会检查Repository接口的所有方法，解析方法的名称，并基于被持久化的对象来试图推测方法的目的。本质上，Spring Data定义了一组小型的领域特定语言（domain-specific language ，DSL），在这里，持久化的细节都是通过Repository方法的签名来描述的。 

Spring Data能够知道这个方法是要查找Spitter的，因为我们使用Spitter 对 JpaRepository进行了参数化。方法名findByUsername确定该方法需要根据username属性相匹配来查找Spitter ，而 username是作为参数传递到方法中来的。另外，因为在方法签名中定义了该方法要返回一个Spitter对象，而不是一个集合，因此它只会查找一个username属性匹配的Spitter 。

findByUsername()方法非常简单，但是Spring Data也能处理更加有意思的方法名称。Repository方法是由一个动词、一个可选的主题（Subject）、关键词By以及一个断言所组成。在findByUsername()这个样例中，动词是find

> 其实还可以写更加复杂的方法，具体内容可参考--》[美] Craig Walls. Spring实战（第4版） (Kindle 位置 5495-5496). 人民邮电出版社. Kindle 版本. 

不过，Spring Data这个小型的DSL依旧有其局限性，有时候通过方法名称表达预期的查询很烦琐，甚至无法实现。如果遇到这种情形的话，Spring Data能够让我们通过@Query注解来解决问题。

#### 声明自定义查询

假设我们想要创建一个Repository方法，用来查找E-mail地址是Gmail邮箱的Spitter。有一种方式就是定义一个findByEmailLike()方法，然后每次想查找Gmail用户的时候就将“%gmail.com”传递进来。不过，更好的方案是定义一个更加便利的findAllGmailSpitters()方法，这样的话，就不用将Email地址的一部分传递进来了：

```java
List<Spitter> findAllGmailSpitters();
```

不过，这个方法并不符合Spring Data的方法命名约定。当Spring Data试图生成这个方法的实现时，无法将方法名的内容与Spitter元模型进行匹配，因此会抛出异常。

如果所需的数据无法通过方法名称进行恰当地描述，那么我们可以使用@Query注解，为Spring Data提供要执行的查询。对于findAllGmailSpitters()方法，我们可以按照如下的方式来使用@Query 注解：

```java
@Query("select s from Spitter s where s.email like '%gmail.com'")
List<Spitter> findAllGmailSpitters();
```

我们依然不需要编写findAllGmailSpitters()方法的实现，只需提供查询即可，让Spring Data JPA知道如何实现这个方法。

对于Spring Data JPA的接口来说，@Query是一种添加自定义查询的便利方式。但是，它仅限于单个JPA查询。如果我们需要更为复杂的功能，无法在一个简单的查询中处理的话，该怎么办呢？

#### 混合自定义的功能

有些时候，我们需要Repository所提供的功能是无法用Spring Data的方法命名约定来描述的，甚至无法用@Query注解设置查询来实现。尽管Spring Data JPA非常棒，但是它依然有其局限性，可能需要我们按照传统的方式来编写Repository方法：也就是直接使用EntityManager。当遇到这种情况的时候，我们是不是要放弃Spring Data JPA，重新按照11.2.2小节中的方式来编写Repository呢？

 简单来说，是这样的。如果你需要做的事情无法通过Spring Data JPA来实现，那就必须要在一个比Spring Data JPA更低的层级上使用JPA。好消息是我们没有必要完全放弃Spring Data JPA。我们只需在必须使用较低层级JPA的方法上，才使用这种传统的方式即可，而对于Spring Data JPA知道该如何处理的功能，我们依然可以通过它来实现。

**当Spring Data JPA为Repository接口生成实现的时候，它还会查找名字与接口相同，并且添加了Impl后缀的一个类。如果这个类存在的话，Spring Data JPA将会把它的方法与Spring Data JPA所生成的方法合并在一起**。对于SpitterRepository接口而言，要查找的类名为SpitterRepositoryImpl 。 

为了阐述该功能，假设我们需要在SpitterRepository中添加一个方法，发表Spittle数量在10,000及以上的Spitter将会更新为Elite状态。使用Spring Data JPA的方法命名约定或使用@Query均没有办法声明这样的方法。最为可行的方案是使用如下的eliteSweep() 方法。

```java
public class SpitterRepositoryImpl implements SpitterSweeper {

  @PersistenceContext
  private EntityManager em;

  public int eliteSweep() {
    String update =
        "UPDATE Spitter spitter " +
        "SET spitter.status = 'Elite' " +
        "WHERE spitter.status = 'Newbie' " +
        "AND spitter.id IN (" +
        "SELECT s FROM Spitter s WHERE (" +
        "  SELECT COUNT(spittles) FROM s.spittles spittles) > 10000" +
        ")";
    return em.createQuery(update).executeUpdate();
  }
}
```

SpitterRepositoryImpl没有什么特殊之处，它使用被注入的EntityManager来完成预期的任务。 

注意， SpitterRepositoryImpl并没有实现SpitterRepository接口。Spring Data JPA负责实现这个接口。SpitterRepositoryImpl（将它与Spring Data的Repository关联起来的是它的名字）实现了SpitterSweeper接口，它如下所示：

```java
public interface SpitterSweeper{
    int eliteSweep();
}
```

我们**还需要确保eliteSweep()方法会被声明在SpitterRepository接口中。要实现这一点，避免代码重复的简单方式就是修改SpitterRepository**，让它扩展SpitterSweeper ：

```java
public interface SpitterRepository
       extends JpaRepository<Spitter, Long>,
               SpitterSweeper {
    ....
}
```

如前所述，Spring Data JPA将实现类与接口关联起来是基于接口的名称。但是，Impl后缀只是默认的做法，如果你想使用其他后缀的话，只需在配置@EnableJpa-Repositories的时候，设置repositoryImplementationPostfix属性即可：

```java
@EnableJpaRepositories(
  basePackages="com.habuma.spittr.db",
  repositoryImplementationPostfix="Helper")
```

如果在XML中使用<jpa:repositories>元素来配置Spring Data JPA的话，我们可以借助repository-impl-postfix属性指定后缀：

```xml
<jpa:repositories base-package="com.habuma.spittr.db"
    repository-impl-postfix="Helper" />
```

其实，混合自定义的意思就是这段话：**当Spring Data JPA为Repository接口生成实现的时候，它还会查找名字与接口相同，并且添加了Impl后缀的一个类。如果这个类存在的话，Spring Data JPA将会把它的方法与Spring Data JPA所生成的方法合并在一起**。

就是所需要添加的功能通过合并一个实现类来完成其所需要的功能，需要注意的是，需要修改xxxRepository接口。

