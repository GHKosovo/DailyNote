## 使用MongoDB持久化数据文档

MongoDB是最为流行的开源文档数据库之一。Spring Data MongoDB提供了三种方式在Spring应用中使用MongoDB：<!--more-->

- 通过注解实现对象-文档映射； 
- 使用 MongoTemplate实现基于模板的数据库访问； 
- 自动化的运行时Repository生成功能。

不过，与Spring Data JPA不同的是，Spring Data MongoDB提供了将Java对象映射为文档的功能。除此之外，Spring Data MongoDB为通用的文档操作任务提供了基于模板的数据访问方式。

### 启动MongoDB

为了有效地使用Spring Data MongoDB，我们需要在Spring配置中添加几个必要的bean。首先，我们需要配置MongoClient，以便于访问MongoDB数据库。同时，我们还需要有一个MongoTemplate bean，实现基于模板的数据库访问。此外，不是必须，但是强烈推荐启用Spring Data MongoDB的自动化Repository生成功能。

```java
package orders.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.core.MongoFactoryBean;
import org.springframework.data.mongodb.core.MongoOperations;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.repository.config.
                                              EnableMongoRepositories;
import com.mongodb.Mongo;

@Configuration
@EnableMongoRepositories(basePackages="orders.db")	
public class MongoConfig {
  @Bean
  public MongoFactoryBean mongo() {	
      MongoFactoryBean mongo = new MongoFactoryBean();
      mongo.setHost("localhost");
      return mongo;
  }

  @Bean
  public MongoOperations mongoTemplate(Mongo mongo) {	
      return new MongoTemplate(mongo, "OrdersDB");
  }

}
```

Repository生成功能。与之类似，@EnableMongoRepositories为MongoDB实现了自动化Repository生成功能；

第一个@Bean方法使用MongoFactoryBean声明了一个Mongo实例。这个bean将Spring Data MongoDB与数据库本身连接了起来（与使用关系型数据时DataSource所做的事情并没有什么区别）。尽管我们可以使用MongoClient 直接创建 Mongo实例，但如果这样做的话，就必须要处理MongoClient构造器所抛出的UnknownHostException异常。在这里，使用Spring Data MongoDB的MongoFactoryBean更加简单。因为它是一个工厂bean，因此MongoFactoryBean会负责构建Mongo实例，我们不必再担心UnknownHostException 异常。

另外一个 @Bean方法声明了MongoTemplate bean，在它构造时，使用了其他@Bean方法所创建的Mongo实例的引用以及数据库的名称。稍后，你将会看到如何使用MongoTemplate来查询数据库。即便不直接使用MongoTemplate，我们也会需要这个bean，因为Repository的自动化生成功能在底层使用了它。

除了直接声明这些bean，我们还可以让配置类扩展AbstractMongo-Configuration 并重载 getDatabaseName() 和 mongo()方法。

```java
package orders.config;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.mongodb.config.
                                           AbstractMongoConfiguration;
import org.springframework.data.mongodb.repository.config.
                                              EnableMongoRepositories;
import com.mongodb.Mongo;
import com.mongodb.MongoClient;

@Configuration
@EnableMongoRepositories("orders.db")
public class MongoConfig extends AbstractMongoConfiguration {

  @Override
  protected String getDatabaseName() {	
    return "OrdersDB";
  }

  @Override
  public Mongo mongo() throws Exception {	
    return new MongoClient();
  }

}
```

这个新的配置类与上一个配置类的功能是相同的，只不过在篇幅上更加简洁。最为显著的区别在于这个配置中没有直接声明MongoTemplate bean，当然它还是会被隐式地创建。我们在这里重载了getDatabaseName()方法来提供数据库的名称。mongo()方法依然会创建一个MongoClient的实例，因为它会抛出Exception，所以我们可以直接使用MongoClient，而不必再使用MongoFactoryBean

MongoDB提供了一个运行配置，也就是说，只要MongoDB服务器运行在本地即可。如果MongoDB服务器运行在其他的机器上，那么可以在创建MongoClient的时候进行指定；另外，MongoDB服务器有可能监听的端口并不是默认的27017。如果是这样的话，在创建MongoClient的时候，还需要指定端口：

```java
public Mongo mongo() throws Exception {
  return new MongoClient("mongodbserver", 37017);
}
```

如果MongoDB服务器运行在生产配置上，我认为你可能还启用了认证功能。在这种情况下，为了访问数据库，我们还需要提供应用的凭证。访问需要认证的MongoDB服务器稍微有些复杂，如下面的程序清单所示。

```java
@Autowired
private Environment env;

@Override
public Mongo mongo() throws Exception {
  MongoCredential credential =
    MongoCredential.createMongoCRCredential(	
        env.getProperty("mongo.username"),
        "OrdersDB",
        env.getProperty("mongo.password").toCharArray());

  return new MongoClient(	
       new ServerAddress("localhost", 37017),
      Arrays.asList(credential));
}
```

为了访问需要认证的MongoDB服务器，MongoClient在实例化的时候必须要有一个MongoCredential的列表。为此我们创建了一个MongoCredential。为了将凭证信息的细节放在配置类外边，它们是通过注入的Environment对象解析得到的。

#### xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:mongo="http://www.springframework.org/schema/data/mongo"	
  xsi:schemaLocation="
    http://www.springframework.org/schema/data/mongo
    http://www.springframework.org/schema/data/mongo/spring-mongo.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <mongo:repositories base-package="orders.db" />	

    <mongo:mongo />	

    <bean id="mongoTemplate"	
        class="org.springframework.data.mongodb.core.MongoTemplate">
      <constructor-arg ref="mongo" />
      <constructor-arg value="OrdersDB" />
    </bean>

</beans>
```

现在Spring Data MongoDB已经配置完成了，我们很快就可以使用它来保存和查询文档了。但首先，需要使用Spring Data MongoDB的对象-文档映射注解为Java领域对象建立到持久化文档的映射关系。

MongoDB并没有提供对象-文档映射的注解。Spring Data MongoDB填补了这一空白，提供了一些将Java类型映射为MongoDB文档的注解。

| 注解      | 描述                                                         |
| --------- | ------------------------------------------------------------ |
| @Document | 标示映射到MongoDB文档上的领域对象                            |
| @Id       | 标示某个域为ID域                                             |
| @DbRef    | 标示某个域要引用其他的文档，这个文档有可能位于另外一个数据库中 |
| @Field    | 为文档域指定自定义的元数据                                   |
| @Version  | 标示某个属性用作版本域                                       |

@Document 和 @Id注解类似于JPA的@Entity 和 @Id注解。我们将会经常使用这两个注解，对于要以文档形式保存到MongoDB数据库的每个Java类型都会使用这两个注解。

```java
package orders;
import java.util.Collection;
import java.util.LinkedHashSet;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

@Document	
 public class Order {

  @Id
  private String id;	

  @Field("client")
  private String customer;	

  private String type;

  private Collection<Item> items = new LinkedHashSet<Item>();

  public String getCustomer() {
    return customer;
  }

  public void setCustomer(String customer) {
    this.customer = customer;
  }

  public String getType() {
    return type;
  }

  public void setType(String type) {
    this.type = type;
  }

  public Collection<Item> getItems() {
    return items;
  }

  public void setItems(Collection<Item> items) {
    this.items = items;
  }

  public String getId() {
    return id;
  }

}
```

同时，需要注意的是items属性，它指的是订单中具体条目的集合。在传统的关系型数据库中，这些条目将会保存在另外的一个数据库表中，通过外键进行应用，items域上很可能还会使用JPA的@OneToMany注解。但在这里，情形完全不同。它始终回事Order文档中Item列表的一个成员，并且会作为文档中的嵌入元素，当然，如果你想指定Item中的某个域如何持久化到文档中，那么可以为对应的Item 属性添加 @Field注解。不过在本例中，并没有必要这样做。

#### 使用MongoTemplate访问MongoDB

```java
@Autowired
MongoOperations mongo;
```

注意，在这里我们将MongoTemplate注入到一个类型为MongoOperations的属性中。MongoOperations 是 MongoTemplate所实现的接口，不使用具体实现是一个好的做法，尤其是在注入的时候。

MongoOperations暴露了多个使用MongoDB文档数据库的方法。在这里，我们不可能讨论所有的方法，但是可以看一下最为常用的几个操作，比如计算文档集合中有多少条文档。使用注入的MongoOperations，我们可以得到Order集合并调用count()来得到数量：

```java
long orderCount = mongo.getCollection("order").count();
```

现在，假设要保存一个新的Order。为了完成这个任务，我们可以调用save() 方法：

```java
Order order = new Order();
... // set properties and add line items
mongo.save(order, "order");
```

对于更高级的查询，我们需要构造Query对象并将其传递给find()方法。例如，要查找所有client域等于“Chuck Wagon"的订单：

```java
List<Order> chucksOrders = mongo.find(Query.query(
    Criteria.where("client").is("Chuck Wagon")), Order.class);
```

在本例中，用来构造Query对象的Criteria只检查了一个域，但是它也可以用来构造更加有意思的查询。比如，我们想要查询Chuck所有通过Web创建的订单：

```java
List<Order> chucksWebOrders = mongo.find(Query.query(
    Criteria.where("customer").is("Chuck Wagon")
            .and("type").is("WEB")), Order.class);
```

通常来讲，我们会将MongoOperations注入到自己设计的Repository类中，并使用它的操作来实现Repository方法。但是，如果你不愿意编写Repository的话，那么Spring Data MongoDB能够自动在运行时生成Repository实现。下面，我们来看一下是如何实现的。

#### 编写MongoDB Responsitory

我们已经通过@EnableMongoRepositories注解启用了Spring Data MongoDB的Repository功能，接下来需要做的就是创建一个接口，Repository实现要基于这个接口来生成。不过，在这里，我们不再扩展JpaRepository，而是要扩展MongoRepository。如下程序清单中的OrderRepository 扩展了 MongoRepository ，为 Order文档提供了基本的CRUD操作。

```java
package orders.db;
import orders.Order;
import org.springframework.data.mongodb.repository.MongoRepository;

public interface OrderRepository
         extends MongoRepository<Order, String> {
}
```

MongoRepository接口有两个参数，第一个是带有@Document注解的对象类型，也就是该Repository要处理的类型。第二个参数是带有@Id注解的属性类型。

| 方法                               | 描述                                             |
| ---------------------------------- | ------------------------------------------------ |
| long count();                      | 返回指定Repository类型的文档数量                 |
| void delete(Iterable<? extends T); | 删除与指定对象关联的所有文档                     |
| void delete(T);                    | 删除与指定对象关联的文档                         |
| void delete(ID);                   | 根据ID删除某一个文档                             |
| void deleteAll();                  | 删除指定Repository类型的所有文档                 |
| boolean exists(Object);            | 如果存在与指定对象相关联的文档，则返回true       |
| boolean exists(ID);                | 如果存在指定ID的文档，则返回true                 |
| List<T> findAll();                 | 返回指定Repository类型的所有文档                 |
| List<T> findAll(Iterable<ID>);     | 返回指定文档ID对应的所有文档                     |
| List<T> findAll(Pageable);         | 为指定的Repository类型，返回分页且排序的文档列表 |
| List<T> findAll(Sort);             | 为指定的Repository类型，返回排序后的所有文档列表 |
| T findOne(ID);                     | 为指定的ID返回单个文档                           |
| Save( terable<s>) ;                | 保存指定Iterable中的所有文档                     |
| save ( < S > );                    | 为给定的对象保存一条文档                         |

表中的方法使用了传递进来和方法返回的泛型。OrderRepository 扩展了 MongoRepository<Order, String> ，那么 T 就映射为 Order ， ID 映射为 String ，而 S映射为所有扩展Order 的类型。

##### 添加自定义的查询方法

```java
public interface OrderRepository
         extends MongoRepository<Order, String> {
  List<Order> findByCustomer(String c);
  List<Order> findByCustomerLike(String c);
  List<Order> findByCustomerAndType(String c, String t);
  List<Order> findByCustomerLikeAndType(String c, String t);
}
```

这里我们有四个新的方法，每一个都是查找满足特定条件的Order对象。其中第一个用来获取customer属性等于传入值的Order列表；第二个方法获取customer属性like传入值的Order列表；接下来方法会返回customer 和 type属性等于传入值的Order对象；最后一个方法与前一个类似，只不过customer在对比的时候使用的是like 而不是 equals 。

具体详情请参考--》[美] Craig Walls. Spring实战（第4版） (Kindle 位置 5806-5809). 人民邮电出版社. Kindle 版本. 

##### 指定查询

@Query能够像在JPA中那样用在MongoDB上。唯一的区别在于针对MongoDB时，@Query会接受一个JSON查询，而不是JPA查询。

例如，假设我们想要查询给定类型的订单，并且要求customer的名称为“Chuck Wagon”。OrderRepository中如下的方法声明能够完成所需的任务：

```java
@Query("{'customer': 'Chuck Wagon', 'type' : ?0}")
List<Order> findChucksOrders(String t);
```

@Query中给定的JSON将会与所有的Order文档进行匹配，并返回匹配的文档。需要注意的是，type属性映射成了“?0”，这表明type属性应该与查询**方法(findChucksOrders)的第零个参数**相等。如果有多个参数的话，它们可以通过“?1”、“?2”等方式进行引用。

##### 混合自定义的功能

之前，我们学习了如何将完全自定义的方法混合到自动生成的Repository中。对于JPA来说，这还涉及到创建一个中间接口来声明自定义的方法，为这些自定义方法创建实现类并修改自动化的Repository接口，使其扩展中间接口。对于Spring Data MongoDB来说，这些步骤都是相同的。

> 具体详情请参考--》[美] Craig Walls. Spring实战（第4版） (Kindle 位置 5829-5832). 人民邮电出版社. Kindle 版本. 

<hr>

##  使用Neo4j操作图数据

Spring Data Neo4j提供了很多与Spring Data JPA和Spring Data MongoDB相同的功能，当然所针对的是Neo4j图数据库。它提供了将Java对象映射到节点和关联关系的注解、面向模板的Neo4j访问方式以及Repository实现的自动化生成功能。

配置Spring Data Neo4j的关键在于声明GraphDatabaseService bean和启用Neo4j Repository自动生成功能。如下的程序清单展现了Spring Data Neo4j所需的基本配置。

```java
package orders.config;
import org.neo4j.graphdb.GraphDatabaseService;
import org.neo4j.graphdb.factory.GraphDatabaseFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.neo4j.config.EnableNeo4jRepositories;
import org.springframework.data.neo4j.config.Neo4jConfiguration;

@Configuration
@EnableNeo4jRepositories(basePackages="orders.db")	
public class Neo4jConfig extends Neo4jConfiguration {

  public Neo4jConfig() {
    setBasePackage("orders");	
  }

  @Bean(destroyMethod="shutdown")
  public GraphDatabaseService graphDatabaseService() {
    return new GraphDatabaseFactory()
              .newEmbeddedDatabase("/tmp/graphdb");	
  }

}
```

@EnableNeo4jRepositories注解能够让Spring Data Neo4j自动生成Neo4j Repository实现。它的basePackages属性设置为orders.db包，这样它就会扫描这个包来查找（直接或间接）扩展Repository标记接口的其他接口。

Neo4jConfig 扩展自 Neo4jConfiguration，后者提供了多个便利的方法来配置Spring Data Neo4j。在这些方法中，就包括setBasePackage() ，它会在 Neo4jConfig的构造器中调用，用来告诉Spring Data Neo4j要在orders包中查找模型类。

这个拼图的最后一部分是定义GraphDatabaseServicebean。在本例中，graphDatabaseService() 方法使用 GraphDatabaseFactory来创建嵌入式的Neo4j数据库。在Neo4j中，嵌入式数据库不要与内存数据库相混淆。在这里，“嵌入式”指的是数据库引擎与应用运行在同一个JVM中，作为应用的一部分，而不是独立的服务器。数据依然会持久化到文件系统中（在本例中，也就是“/tmp/graphdb”中）。

作为另外的一种方式，你可能会希望配置GraphDatabaseService连接远程的Neo4j服务器。如果spring-data-neo4j-rest库在应用的类路径下，那么我们就可以配置SpringRestGraphDatabase，它会通过RESTful API来访问远程的Neo4j数据库：

```java
@Bean(destroyMethod="shutdown")
public GraphDatabaseService graphDatabaseService() {
  return new SpringRestGraphDatabase(
      "http://graphdbserver:7474/db/data/");
}
```

如上所示，SpringRestGraphDatabase在配置时，假设远程的数据库并不需要认证。但是，在生产环境的配置中，当创建SpringRestGraphDatabase的时候，我们可能希望提供应用的凭证：

```java
@Bean(destroyMethod="shutdown")
public GraphDatabaseService graphDatabaseService(Environment env) {
  return new SpringRestGraphDatabase(
      "http://graphdbserver:7474/db/data/",
      env.getProperty("db.username"), env.getProperty("db.password"));
}
```

在这里，凭证是通过注入的Environment获取到的，避免了在配置类中的硬编码。 Spring Data Neo4j同时还提供了XML命名空间。如果你更愿意在XML中配置Spring Data Neo4j的话，那可以使用该命名空间中的\<neo4j:config> 和 \<neo4j:repositories>元素。在功能上，与Java配置是相同的。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:neo4j="http://www.springframework.org/schema/data/neo4j"
  xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/data/neo4j
   http://www.springframework.org/schema/data/neo4j/spring-neo4j.xsd">
  <neo4j:config	
      storeDirectory="/tmp/graphdb"
      base-package="orders" />

  <neo4j:repositories base-package="orders.db" />	
</beans>
```

如果要配置Spring Data Neo4j访问远程的Neo4j服务器，我们所需要做的就是声明SpringRestGraphDatabasebean，并设置<neo4j:config> 的 graphDatabaseService 属性：

```xml
<neo4j:config base-package="orders"
              graphDatabaseService="graphDatabaseService" />
<bean id="graphDatabaseService" class=
        "org.springframework.data.neo4j.rest.SpringRestGraphDatabase">
  <constructor-arg value="http://graphdbserver:7474/db/data/" />
  <constructor-arg value="db.username" />
  <constructor-arg value="db.password" />
</bean>
```

#### 使用注解标注图实体

Neo4j定义了两种类型的实体：节点（node）和关联关系（relationship）。一般来讲，节点反映了应用中的事物，而关联关系定义了这些事物是如何联系在一起的。 

Spring Data Neo4j提供了多个注解，它们可以应用在模型类型及其域上，实现Neo4j中的持久化。

| 注　　解            | 描　　述                                                     |
| ------------------- | ------------------------------------------------------------ |
| @NodeEntity         | 将Java类型声明为节点实体                                     |
| @RelationshipEntity | 将Java类型声明为关联关系实体                                 |
| @StartNode          | 将某个属性声明为关联关系实体的开始节点                       |
| @EndNode            | 将某个属性声明为关联关系实体的结束节点                       |
| @Fetch              | 将实体的属性声明为立即加载                                   |
| @GraphId            | 将某个属性设置为实体的ID域（这个域的类型必须是 Long          |
| @GraphProperty      | 明确声明某个属性                                             |
| @GraphTraversal     | 声明某个属性会自动提供一个iterable元素，这个元素是图遍历所构建的 |
| @Indexed            | 声明某个属性应该被索引                                       |
| @Labels             | 为@NodeEntity声明标签                                        |
| @Query              | 声明某个属性会自动提供一个iterable元素，这个元素是执行给定的Cypher查询所构建的 |
| @QueryResult        | 声明某个Java或接口能够持有查询的结果                         |
| @RelatedTo          | 通过某个属性，声明当前的 @NodeEntity与另外一个@NodeEntity之间的关联关系 |
| @RelatedToVia       | 在  @NodeEntity上声明某个属性，指定其引用该节点所属的某一个@RelationshipEntity |
| @RelationshipType   | 将某个域声明为关联实体类型                                   |
| @ResultColumn       | 在带有@QueryResult注解的类型上，将某个属性声明为获取查询结果集中的某个特定列 |

为了了解如何使用其中的某些注解，我们会将其应用到订单/条目样例中。 在该样例中，数据建模的一种方式就是将订单设定为一个节点，它会与一个或多个条目关联。图12.2以图的形式描述了这种模型。

![图12.2](https://drek4537l1klr.cloudfront.net/walls5/Figures/12fig02.jpg)

图12.2　连接两个节点的简单关联关系，关系本身不包含任何属性

为了将订单指定为节点，我们需要为Order 类添加 @NodeEntity注解。如下的程序清单展现了带有@NodeEntity 注解的 Order类，它还包含了表12.3中的几个其他注解。

```java
package orders;
import java.util.LinkedHashSet;
import java.util.Set;
import org.springframework.data.neo4j.annotation.GraphId;
import org.springframework.data.neo4j.annotation.NodeEntity;
import org.springframework.data.neo4j.annotation.RelatedTo;

@NodeEntity	
public class Order {

  @GraphId	
  private Long id;
  private String customer;
  private String type;

  @RelatedTo(type="HAS_ITEMS")	
  private Set<Item> items = new LinkedHashSet<Item>();

  ...

}
```

除了类级别上的@NodeEntity，还要注意id属性上使用了@GraphId注解。Neo4j上的所有实体必要要有一个图ID。这大致类似于JPA @Entity以及MongoDB @Document 类中使用 @Id注解的属性。在这里，@GraphId注解标注的属性必须是Long 类型。 

customer 和 type属性上没有任何注解。只要这些属性不是瞬态的，它们都会成为数据库中节点的属性。 

items属性上使用了@RelatedTo注解，这表明Order 与一个 Item 的 Set存在关联关系。type属性实际上就是为关联关系建立了一个文本标记。它可以设置成任意的值，但通常会给定一个易于人类阅读的文本，用来简单描述这个关联关系的特征。稍后，你将会看到如何将这个标记用在查询中，实现跨关联关系的查询。

就 Item本身来说，下面展现了如何为其添加注解实现图的持久化。

```java
package orders;
import org.springframework.data.neo4j.annotation.GraphId;
import org.springframework.data.neo4j.annotation.NodeEntity;

@NodeEntity	
public class Item {

  @GraphId	
  private Long id;
  private String product;
  private double price;
  private int quantity;

  ...

}
```

类似于 Order ， Item 也使用了 @NodeEntity注解，将其标记为一个节点。它同时也有一个Long类型的属性，借助@GraphId注解将其标注为节点的图ID，而product 、 price 以及 quantity属性均会作为图数据库中节点的属性。

Order 和 Item之间的关联关系很简单，关系本身并不包含任何的数据。因此，@RelatedTo注解就足以定义关联关系。但是，并不是所有的关联关系都这么简单。 

让我们重新考虑该如何为数据建模，从而学习如何使用更为复杂的关联关系。在当前的数据模型中，我们将条目和产品的信息组合到了Item类中。但是，当我们重新考虑的时候，会发现订单会与一个或多个产品相关联。订单与产品之间的关系构成了订单的一个条目。图12.3描述了另外一种在图中建模数据的方式。

![图12.3](https://drek4537l1klr.cloudfront.net/walls5/Figures/12fig03.jpg)

图12.3　关联关系实体自身具有属性

在这个新的模型中，订单中产品的数量是条目中的一个属性，而产品本身是另外一个概念。与前面一样，订单和产品都是节点，而条目是关联关系。因为现在的条目必须要包含一个数量值，关联关系不像前面那么简单。我们需要定义一个类来代表条目，比如如下程序清单所示的LineItem 。

```java
package orders;
import org.springframework.data.neo4j.annotation.EndNode;
import org.springframework.data.neo4j.annotation.GraphId;
import org.springframework.data.neo4j.annotation.RelationshipEntity;
import org.springframework.data.neo4j.annotation.StartNode;

@RelationshipEntity(type="HAS_LINE_ITEM_FOR")	
public class LineItem {

  @GraphId	
  private Long id;

  @StartNode	
  private Order order;

  @EndNode	
  private Product product;

  private int quantity;

  ...

}
```

Order类通过@NodeEntity注解将其标示为一个节点，而LineItem类则使用了@RelationshipEntity 注解。 LineItem同样也有一个id属性标注了@GraphId注解，不管是节点实体还是关联关系实体，都必须要有一个图ID，而且其类型必须为Long 。 

关联关系实体的特殊之处在于它们连接了两个节点。@StartNode 和 @EndNode注解用在定义关联关系两端的属性上。在本例中，Order是开始节点，Product是结束节点。 

最后， LineItem 类有一个 quantity属性，当关联关系创建的时候，它会持久化到数据库中。

领域对象已经添加了注解，现在就可以保存与读取节点和关联关系了。我们首先看一下如何使用Spring Data Neo4j中的Neo4jTemplate实现面向模板的数据访问。

#### 使用Neo4jTemplate

Spring Data MongoDB提供了MongoTemplate实现基于模板的MongoDB持久化，与之类似，Spring Data Neo4j提供了Neo4jTemplate来操作Neo4j图数据库中的节点和关联关系。如果你已经按照前面的方式配置了Spring Data Neo4j，在Spring应用上下文中就已经具备了一个Neo4jTemplatebean。接下来需要做的就是将其注入到任意想使用它的地方。

Spring Data MongoDB提供了MongoTemplate实现基于模板的MongoDB持久化，如果你已经按照前面的方式配置了Spring Data Neo4j，在Spring应用上下文中就已经具备了一个Neo4jTemplatebean。接下来需要做的就是将其注入到任意想使用它的地方。

```java
@Autowired
private Neo4jOperations neo4j;
```

我们想借助Neo4jTemplate完成的最基本的一件事情可能就是将某个对象保存为节点。假设这个对象已经使用了@NodeEntity注解，那么我们可以按照如下的方式来使用save() 方法：

```java
Order order = ...;
Order savedOrder = neo4j.save(order);
```

#### 创建自动化的Neo4j Respository

我们已经将@EnableNeo4jRepositories添加到了配置中，所以Spring Data Neo4j已经配置为支持自动化生成Repository的功能。我们所需要做的就是编写接口，如下的OrderRepository就是很好的起点：

```java
package orders.db;
import orders.Order;
import org.springframework.data.neo4j.repository.GraphRepository;

public interface OrderRepository extends GraphRepository<Order> {}
```

与其他的Spring Data项目一样，Spring Data Neo4j会为扩展Repository接口的其他接口生成Repository方法实现。在本例中，OrderRepository 扩展了 GraphRepository，而后者又间接扩展了Repository接口。因此，Spring Data Neo4j将会在运行时创建OrderRepository 的实现。 

注意， GraphRepository 使用 Order进行了参数化，也就是这个Repository所要使用的实体类型。因为Neo4j要求图ID的类型为Long，因此在扩展GraphRepository的时候，没有必要再去指定ID类型。

现在，我们就能够使用很多通用的CRUD操作，这与JpaRepository 和 MongoRepository所提供的功能类似。表12.4描述了扩展GraphRepository所能够得到的方法。

表12.4　通过扩展GraphRepository，Repository接口能够继承多个CRUD操作，它们会由Spring Data Neo4j自动实现

| 方　　法                                                  | 描　　述                             |
| --------------------------------------------------------- | ------------------------------------ |
| long count();                                             | 返回在数据库中，目标类型有多少实体   |
| void delete(Iterable<?extendsT>);                         | 删除多个实体                         |
| void delete(Long id);                                     | 根据ID，删除一个实体                 |
| void delete(T);                                           | 删除一个实体                         |
| void deleteAll();                                         | 删除目标类型的所有实体               |
| boolean exists(Long id);                                  | 根据指定的ID，检查实体是否存在       |
| EndResult<T> findAll();                                   | 获取目标类型的所有实体               |
| Iterable<T> findAll(Iterable<Long>);                      | 根据给定的ID，获取目标类型的实体     |
| Page<T> findAll(Pageable);                                | 返回目标类型分页和排序后的实体列表   |
| EndResult<T> findAll(Sort);                               | 返回目标类型排序后的实体列表         |
| EndResult<T> findAllBySchemaPropertyValue(String,Object); | 返回指定属性匹配给定值的所有实体     |
| Iterable<T> findAllByTraversal(N, TraversalDescription);  | 返回从某个节点开始，图遍历到达的节点 |
| T findBySchemaPropertyValue (String,Object);              | 返回指定属性匹配给定值的一个实体     |
| T findOne(Long);                                          | 根据ID，获得某一个实体               |
| EndResult<T> query(String, Map<String,Object>);           | 返回匹配给定Cypher查询的所有实体     |
| Iterable<T> save(Iterable<T>);                            | 保存多个实体                         |
| S save(S);                                                | 保存一个实体                         |

如下的代码能够保存一个Order 实体：

```java
Order savedOrder = orderRepository.save(order);
```

当实体保存之后，save()方法将会返回被保存的实体，如果之前它使用@GraphId注解的属性值为null的话，此时这个属性将会填充上值。

##### 添加查询方法

```java
package orders.db;
import java.util.List;
import orders.Order;
import org.springframework.data.neo4j.repository.GraphRepository;

public interface OrderRepository extends GraphRepository<Order> {

  List<Order> findByCustomer(String customer);	
  List<Order> findByCustomerAndType(String customer, String type);

}
```

这里，我们添加了两个方法。其中一个会查询customer属性等于给定String 值的 Order节点。另外一个方法与之类似，但是除了匹配customer属性以外，Order 节点的 type属性必须还要等于给定的类型值。

我们之前已经讨论过查询方法的命名约定，所以这里没有必要再进行深入地讨论。可以翻看之前学习Spring Data JPA的章节，重新温习如何编写这些方法。

##### 指定自定义查询

当命名约定无法满足需求时，我们还可以为方法添加@Query注解，为其指定自定义的查询。我们之前已经见过@Query注解。MongoDB中，我们使用它来指定匹配JSON的查询。但是，在使用Spring Data Neo4j的时候，我们必须指定Cypher查询：

```java
@Query("match (o:Order)-[:HAS_ITEMS]->(i:Item) " +
       "where i.product='Spring in Action' return o")
List<Order> findSiAOrders();
```

在这里， findSiAOrders()方法上使用了@Query注解，并设置了一个Cypher查询，它会查找与Item 关联并且 product属性等于“Spring in Action”的所有Order 节点。

##### 混合自定义的Responsitory

> 具体内容参考--》[美] Craig Walls. Spring实战（第4版） (Kindle位置6100). 人民邮电出版社. Kindle 版本. 

## 使用Redis操作key-value数据

Redis是一种特殊类型的数据库，它被称之为key-value存储。顾名思义，key-value存储保存的是键值对。实际上，key-value存储与哈希Map有很大的相似性。可以不太夸张地说，它们就是持久化的哈希Map。

当你思考这一点的时候，可能会意识到，对于哈希Map或者key-value存储来说，其实并没有太多的操作。我们可以将某个value存储到特定的key上，并且能够根据特定key，获取value。差不多也就是这样了。因此，Spring Data的自动Repository生成功能并没有应用到Redis上。不过，Spring Data的另外一个关键特性，也就是面向模板的数据访问，能够在使用Redis的时候，为我们提供帮助。

Spring Data Redis包含了多个模板实现，用来完成Redis数据库的数据存取功能。

### 连接到Redis

Redis连接工厂会生成到Redis数据库服务器的连接。Spring Data Redis为四种Redis客户端实现提供了连接工厂：

- JedisConnectionFactory
- JredisConnectionFactory
- LettuceConnectionFactory
- SrpConnectionFactory

配置JedisConnectionFactory bean:

```java
@Bean
public RedisConnectionFactory redisCF() {
  JedisConnectionFactory cf = new JedisConnectionFactory();
  cf.setHostName("redis-server");
  cf.setPort(7379);
  cf.setPassword("foobared");
  return cf;
}
```

### 使用RedisTemplate

顾名思义，Redis连接工厂会生成到Redis key-value存储的连接（以RedisConnection的形式）。借助RedisConnection，可以存储和读取数据。例如，我们可以获取连接并使用它来保存一个问候信息，如下所示：

```java
RedisConnectionFactory cf = ...;
RedisConnection conn = cf.getConnection();
conn.set("greeting".getBytes(), "Hello World".getBytes());
```

与之类似，我们还可以使用RedisConnection来获取之前存储的问候信息：

```java
byte[] greetingBytes = conn.get("greeting".getBytes());
String greeting = new String(greetingBytes);
```

为了不直接使用字节数组，与其他的Spring Data项目类似，Spring Data Redis以模板的形式提供了较高等级的数据访问方案。实际上，Spring Data Redis提供了两个模板：

- RedisTemplate
- StringRedisTemplate

RedisTemplate可以极大地简化Redis数据访问，能够让我们持久化各种类型的key和value，并不局限于字节数组。在认识到key和value通常是String类型之后，StringRedisTemplate 扩展了 RedisTemplate ，只关注 String 类型。

假设我们已经有RedisConnectionFactory,那么可以这样构造RedisTemplate:

```java
RedisConnectionFactory cf = ...;
RedisTemplate<String, Product> redis =
    new RedisTemplate<String, Product>();
redis.setConnectionFactory(cf);
```

注意， RedisTemplate使用两个类型进行了参数化。第一个是key的类型，第二个是value的类型。在这里所构建的RedisTemplate中，将会保存Product对象作为value，并将其赋予一个String类型的key。

如果你所使用的value和key都是String类型，那么可以考虑使用StringRedisTemplate 来代替 RedisTemplate ：

```java
RedisConnectionFactory cf = ...;
StringRedisTemplate redis = new StringRedisTemplate(cf);
```

注意，与 RedisTemplate 不同， StringRedisTemplate有一个接受RedisConnectionFactory的构造器，因此没有必要在构建后再调用setConnectionFactory() 。

尽管这并非必须的，但是如果你经常使用RedisTemplate 或 StringRedisTemplate的话，你可以考虑将其配置为bean，然后注入到需要的地方。如下就是一个声明RedisTemplate

```java
@Bean
public RedisTemplate<String, Product>
                            redisTemplate(RedisConnectionFactory cf) {
  RedisTemplate<String, Product> redis =
      new RedisTemplate<String, Product>();
  redis.setConnectionFactory(cf);
  return redis;
}
```

如下是声明StringRedisTemplate bean的@Bean 方法：

```java
@Bean
public StringRedisTemplate
                      stringRedisTemplate(RedisConnectionFactory cf) {
  return new StringRedisTemplate(cf);
}
```

有了 RedisTemplate （或 StringRedisTemplate）之后，我们就可以开始保存、获取以及删除key-value条目了。RedisTemplate的大多数操作都是表12.5中的子API提供的。

|     方　　法     |         子API接口         |                 描　　述                  |
| :--------------: | :-----------------------: | :---------------------------------------: |
|  opsForValue()   |   ValueOperations<K, V>   |           操作具有简单值的条目            |
|   opsForList()   |   ListOperations<K, V>    |           操作具有list值的条目            |
|   opsForSet()    |    SetOperations<K, V>    |            操作具有set值的条目            |
|   opsForZSet()   |   ZSetOperations<K, V>    |     操作具有ZSet值（排序的set）的条目     |
|   opsForHash()   | HashOperations<K, HK, HV> |           操作具有hash值的条目            |
| boundValueOps(K) | BoundValueOperations<K,V> | 以绑定指定key的方式，操作具有简单值的条目 |
| boundListOps(K)  | BoundListOperations<K,V>  | 以绑定指定key的方式，操作具有list值的条目 |

表中的子API能够通过RedisTemplate（和 StringRedis-Template）进行调用。其中每个子API都提供了使用数据条目的操作，基于value中所包含的是单个值还是一个值的集合它们会有所差别。

#### 使用简单的值

假设我们想通过RedisTemplate<String, Product> 保存 Product，其中key是sku属性的值。如下的代码片段展示了如何借助opsForValue()方法完成该功能：

```java
redis.opsForValue().set(product.getSku(), product);
```

#### 使用List类型的值

使用List类型的value与之类似，只需使用opsForList()方法即可。例如，我们可以在一个List类型的条目尾部添加一个值：

```java
redis.opsForList().rightPush("cart", product);
```

### 使用key和value的序列化器

当某个条目保存到Redis key-value存储的时候，key和value都会使用Redis的序列化器（serializer）进行序列化。Spring Data Redis提供了多个这样的序列化器，包括：

- GenericToStringSerializer：使用Spring转换服务进行序列化； 
- JacksonJsonRedisSerializer：使用Jackson 1，将对象序列化为JSON；
- Jackson2JsonRedisSerializer：使用Jackson 2，将对象序列化为JSON； 
- JdkSerializationRedisSerializer：使用Java序列化； 
- OxmSerializer：使用Spring O/X映射的编排器和解排器（marshaler和unmarshaler）实现序列化，用于XML序列化； 
- StringRedisSerializer：序列化String类型的key和value。

这些序列化器都实现了RedisSerializer接口，如果其中没有符合需求的序列化器，那么你还可以自行创建。

RedisTemplate 会使用 JdkSerializationRedisSerializer，这意味着key和value都会通过Java进行序列化。StringRedisTemplate默认会使用StringRedis-Serializer，这在我们的预料之中，它实际上就是实现String 与 byte数组之间的相互转换。这些默认的设置适用于很多的场景，但有时候你可能会发现使用一个不同的序列化器也是很有用处的。

例如，假设当使用RedisTemplate的时候，我们希望将Product类型的value序列化为JSON，而key是String 类型。 RedisTemplate 的 setKeySerializer() 和 setValueSerializer()方法就需要如下所示：

```java
@Bean
public RedisTemplate<String, Product>
       redisTemplate(RedisConnectionFactory cf) {
  RedisTemplate<String, Product> redis =
      new RedisTemplate<String, Product>();
  redis.setConnectionFactory(cf);
  redis.setKeySerializer(new StringRedisSerializer());
  redis.setValueSerializer(
      new Jackson2JsonRedisSerializer<Product>(Product.class));
  return redis;
}
```

在这里，我们设置RedisTemplate在序列化key的时候，使用StringRedisSerializer，并且也设置了在序列化Product的时候，使用Jackson2JsonRedisSerializer

