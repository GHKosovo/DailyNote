## 启用对缓存的支持

Spring对缓存的支持有两种方式： 

- 注解驱动的缓存 
- XML声明的缓存<!--more-->

使用Spring的缓存抽象时，最为通用的方式就是在方法上添加@Cacheable 和 @CacheEvict注解。在本章中，大多数内容都会使用这种类型的声明式注解。在13.3小节中，我们会看到如何使用XML来声明缓存边界。

在往 bean上添加缓存注解之前，必须要启用Spring对注解驱动缓存的支持。如果我们使用Java配置的话，那么可以在其中的一个配置类上添加@EnableCaching，这样的话就能启用注解驱动的缓存。以下展现了如何实际使用@EnableCaching 。

```java
package com.habuma.cachefun;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.concurrent.ConcurrentMapCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableCaching	
public class CachingConfig {

  @Bean
  public CacheManager cacheManager() {	
    return new ConcurrentMapCacheManager();
  } 

}
```

如果以XML的方式配置应用的话，那么可以使用Spring cache命名空间中的\<cache:annotation-driven>元素来启用注解驱动的缓存。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:cache="http://www.springframework.org/schema/cache"
  xsi:schemaLocation="
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache.xsd">

  <cache:annotation-driven />	

  <bean id="cacheManager" class=
  "org.springframework.cache.concurrent.ConcurrentMapCacheManager" />	

</beans>
```

其实在本质上，@EnableCaching 和 \<cache:annotation-driven>的工作方式是相同的。它们都会创建一个切面（aspect）并触发Spring缓存注解的切点（pointcut）。根据所使用的注解以及缓存的状态，这个切面会从缓存中获取数据，将数据添加到缓存之中或者从缓存中移除某个值。

你可能已经注意到了，它们不仅仅启用了注解驱动的缓存，还声明了一个缓存管理器（cache manager）的bean。缓存管理器是Spring缓存抽象的核心，它能够与多个流行的缓存实现进行集成。

在本例中，声明了ConcurrentMapCacheManager，这个简单的缓存管理器使用java.util.concurrent.ConcurrentHashMap作为其缓存存储。它非常简单，因此对于开发、测试或基础的应用来讲，这是一个很不错的选择。但它的缓存存储是基于内存的，所以它的生命周期是与应用关联的，对于生产级别的大型企业级应用程序，这可能并不是理想的选择。

幸好，有多个很棒的缓存管理器方案可供使用。让我们看一下几个最为常用的缓存管理器。

### 配置缓存管理器

Spring 3.1内置了五个缓存管理器实现，如下所示：

- SimpleCacheManager 
- NoOpCacheManager 
- ConcurrentMapCacheManager 
- CompositeCacheManager 
- EhCacheCacheManager

Spring 3.2引入了另外一个缓存管理器，这个管理器可以用在基于JCache（JSR-107）的缓存提供商之中。除了核心的Spring框架，Spring Data又提供了两个缓存管理器：

- RedisCacheManager（来自于Spring Data Redis项目） 
- GemfireCacheManager（来自于Spring Data GemFire项目）

所以可以看到，在为Spring的缓存抽象选择缓存管理器时，我们有很多可选方案。具体选择哪一个要取决于想要使用的底层缓存供应商。每一个方案都可以为应用提供不同风格的缓存，其中有一些会比其他的更加适用于生产环境。尽管所做出的选择会影响到数据如何缓存，但是Spring声明缓存的方式上并没有什么差别。

#### 使用Ehcache缓存

Ehcache是最为流行的缓存供应商之一。Ehcache网站上说它是“Java领域应用最为广泛的缓存”。鉴于它的广泛采用，Spring提供集成Ehcache的缓存管理器是很有意义的。这个缓存管理器也就是EhCacheCacheManager 。

Java配置EhCacheCacheManager方式：

```java
package com.habuma.cachefun;
import net.sf.ehcache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.ehcache.EhCacheCacheManager;
import org.springframework.cache.ehcache.EhCacheManagerFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;

@Configuration
@EnableCaching
public class CachingConfig {

  @Bean
  public EhCacheCacheManager cacheManager(CacheManager cm) {	
     return new EhCacheCacheManager(cm);
  }

  @Bean
  public EhCacheManagerFactoryBean ehcache() {	
     EhCacheManagerFactoryBean ehCacheFactoryBean =
        new EhCacheManagerFactoryBean();
    ehCacheFactoryBean.setConfigLocation(
        new ClassPathResource("com/habuma/spittr/cache/ehcache.xml"));
    return ehCacheFactoryBean;
  }
}
```

在程序清单中，cacheManager()方法创建了一个EhCacheCacheManager的实例，这是通过传入Ehcache CacheManager实例实现的。在这里，稍微有点诡异的注入可能会让人感觉迷惑，这是因为Spring和EhCache都定义了CacheManager类型。需要明确的是，EhCache的CacheManager要被注入到Spring的EhCacheCacheManager（Spring CacheManager的实现）之中。 

我们需要使用EhCache的CacheManager来进行注入，所以必须也要声明一个CacheManager bean。为了对其进行简化，Spring提供了EhCacheManager-FactoryBean来生成EhCache的CacheManager 。方法 ehcache()会创建并返回一个EhCacheManagerFactoryBean实例。因为它是一个工厂bean（也就是说，它实现了Spring的FactoryBean接口），所以注册在Spring应用上下文中的并不是EhCacheManagerFactoryBean的实例，而是CacheManager的一个实例，因此适合注入到EhCacheCacheManager之中

除了在Spring中配置的bean，还需要有针对EhCache的配置。EhCache为XML定义了自己的配置模式，我们需要在一个XML文件中配置缓存，该文件需要符合EhCache所定义的模式。在创建EhCacheManagerFactoryBean的过程中，需要告诉它EhCache配置文件在什么地方。在这里通过调用setConfigLocation()方法，传入ClassPath-Resource，用来指明EhCache XML配置文件相对于根类路径（classpath）的位置。

至于ehcache.xml文件的内容，不同的应用之间会有所差别，但是至少需要声明一个最小的缓存。例如，如下的EhCache配置声明一个名为spittleCache的缓存，它最大的堆存储为50MB，存活时间为100秒。

```java
<ehcache>
  <cache name="spittleCache"
          maxBytesLocalHeap="50m"
          timeToLiveSeconds="100">
  </cache>
</ehcache>
```

显然，这是一个基础的EhCache配置。在你的应用之中，可能需要使用EhCache所提供的丰富的配置选项。参考EhCache的文档以了解调优EhCache配置的细节，地址是http://ehcache.org/documentation/configuration 。

#### 使用redis缓存

Redis可以用来为Spring缓存抽象机制存储缓存条目，Spring Data Redis提供了RedisCacheManager ，这是 CacheManager的一个实现。RedisCacheManager会与一个Redis服务器协作，并通过RedisTemplate将缓存条目存储到Redis中。

```java
package com.myapp;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.jedis
                                      .JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;

@Configuration
@EnableCaching
public class CachingConfig {

  @Bean
  public CacheManager cacheManager(RedisTemplate redisTemplate) {
    return new RedisCacheManager(redisTemplate);	
   }

  @Bean
  public JedisConnectionFactory redisConnectionFactory() {	
    JedisConnectionFactory jedisConnectionFactory =
           new JedisConnectionFactory();
   jedisConnectionFactory.afterPropertiesSet();
   return jedisConnectionFactory;
  }

  @Bean
  public RedisTemplate<String, String> redisTemplate(	
           RedisConnectionFactory redisCF) {
    RedisTemplate<String, String> redisTemplate =
            new RedisTemplate<String, String>();
    redisTemplate.setConnectionFactory(redisCF);
    redisTemplate.afterPropertiesSet();
    return redisTemplate;
  }

}
```

#### 使用多个缓存管理器

我们并不是只能有且仅有一个缓存管理器。如果你很难确定该使用哪个缓存管理器，或者有合法的技术理由使用超过一个缓存管理器的话，那么可以尝试使用Spring的CompositeCacheManager

CompositeCacheManager要通过一个或更多的缓存管理器来进行配置，它会迭代这些缓存管理器，以查找之前所缓存的值。以下的程序清单展现了如何创建CompositeCacheManager bean，它会迭代JCacheCacheManager 、 EhCacheCache-Manager 和 RedisCacheManager 。

```java
@Bean
public CacheManager cacheManager(
      net.sf.ehcache.CacheManager cm,
      javax.cache.CacheManager jcm) {

  CompositeCacheManager cacheManager = new CompositeCacheManager();	
  List<CacheManager> managers = new ArrayList<CacheManager>();
  managers.add(new JCacheCacheManager(jcm));
  managers.add(new EhCacheCacheManager(cm))
  managers.add(new RedisCacheManager(redisTemplate()));
  cacheManager.setCacheManagers(managers);	
  return cacheManager;
}
```

当查找缓存条目时，CompositeCacheManager 首先会从 JCacheCacheManager开始检查JCache实现，然后通过EhCacheCacheManager检查Ehcache，最后会使用RedisCacheManager来检查Redis，完成缓存条目的查找。

在配置完缓存管理器并启用缓存后，就可以在bean方法上应用缓存规则了。让我们看一下如何使用Spring的缓存注解来定义缓存边界。

## 为方法添加注解以支持缓存

如前文所述，Spring的缓存抽象在很大程度上是围绕切面构建的。在Spring中启用缓存时，会创建一个切面，它触发一个或更多的Spring的缓存注解。表13.1列出了Spring所提供的缓存注解。

表13.1中的所有注解都能运用在方法或类上。当将其放在单个方法上时，注解所描述的缓存行为只会运用到这个方法上。如果注解放在类级别的话，那么缓存行为就会应用到这个类的所有方法上。

表13.1　Spring提供了四个注解来声明缓存规则

| 注　　解    | 描　　述                                                     |
| ----------- | ------------------------------------------------------------ |
| @Cacheable  | 表明Spring在调用方法之前，首先应该在缓存中查找方法的返回值。如果这个值能够找到，就会返回缓存的值。否则的话，这个方法就会被调用，返回值会放到缓存之中 |
| @CachePut   | 表明Spring应该将方法的返回值放到缓存中。在方法的调用前并不会检查缓存，方法始终都会被调用 |
| @CacheEvict | 表明Spring应该在缓存中清除一个或多个条目                     |
| @Caching    | 这是一个分组的注解，能够同时应用多个其他的缓存注解           |

### 填充缓存

@Cacheable和@CachePut有一些共有的属性

| 属         性 | 类          型 | 描          述                                               |
| ------------- | -------------- | ------------------------------------------------------------ |
| value         | String[]       | 要使用的缓存名称                                             |
| condition     | String         | SpEL表达式，如果得到的值是false的话，不会将缓存应用到方法调用上 |
| key           | String         | SpEL表达式，用来计算自定义的缓存key                          |
| unless        | String         | SpEL表达式，如果得到的值是true的话，返回值不会放到缓存之中   |

在最简单的情况下，在@Cacheable 和 @CachePut的这些属性中，只需使用value属性指定一个或多个缓存即可。例如，考虑SpittleRepository 的 findOne()方法。在初始保存之后，Spittle就不会再发生变化了。如果有的Spittle比较热门并且会被频繁请求，反复地在数据库中进行获取是对时间和资源的浪费。通过在findOne()方法上添加@Cacheable注解，如下面的程序清单所示，能够确保将Spittle保存在缓存中，从而避免对数据库的不必要访问。

```java
@Cacheable("spittleCache")	
public Spittle findOne(long id) {
  try {
    return jdbcTemplate.queryForObject(
        SELECT_SPITTLE_BY_ID,
        new SpittleRowMapper(),
        id);
  } catch (EmptyResultDataAccessException e) {
    return null;
  }
}
```

当 findOne()被调用时，缓存切面会拦截调用并在缓存中查找之前以名spittleCache存储的返回值。缓存的key 是传递到 findOne() 方法中的 id参数。如果按照这个key能够找到值的话，就会返回找到的值，方法不会再被调用。如果没有找到值的话，那么就会调用这个方法，并将返回值放到缓存之中，为下一次调用findOne()方法做好准备。

在程序清单13.6中，@Cacheable注解被放到了JdbcSpittleRepository 的 findOne()方法实现上。这样能够起作用，但是缓存的作用只限于JdbcSpittleRepository这个实现类中，SpittleRepository的其他实现并没有缓存功能，除非也为其添加上@Cacheable注解。因此，可以考虑将注解添加到SpittleRepository的方法声明上，而不是放在实现类中：

```java
@Cacheable("spittleCache")
Spittle findOne(long id);
```

当为接口方法添加注解后，@Cacheable 注解会被 SpittleRepository的所有实现继承，这些实现类都会应用相同的缓存规则。

#### 将值放到缓存中

@Cacheable会条件性地触发对方法的调用，这取决于缓存中是不是已经有了所需要的值，对于所注解的方法，@CachePut采用了一种更为直接的流程。带有@CachePut注解的方法始终都会被调用，而且它的返回值也会放到缓存中。这提供一种很便利的机制，能够让我们在请求之前预先加载缓存。

例如，当一个全新的Spittle 通过 SpittleRepository 的 save()方法保存之后，很可能马上就会请求这条记录。所以，当save()方法调用后，立即将Spittle塞到缓存之中是很有意义的，这样当其他人通过findOne()对其进行查找时，它就已经准备就绪了。为了实现这一点，可以在save()方法上添加@CachePut注解，如下所示：

```java
@CachePut("spittleCache")
Spittle save(Spittle spittle);
```

当 save()方法被调用时，它首先会做所有必要的事情来保存Spittle，然后返回的Spittle 会被放到 spittleCache 缓存中。

在这里只有一个问题：缓存的key。如前文所述，默认的缓存key要基于方法的参数来确定。因为save()方法的唯一参数就是Spittle，所以它会用作缓存的key。将Spittle放在缓存中，而它的缓存key恰好是同一个Spittle，这是不是有一点诡异呢？

显然，在这个场景中，默认的缓存key并不是我们想要的。我们需要的缓存key是新保存Spittle的ID，而不是Spittle本身。所以，在这里需要指定一个key而不是使用默认的key。让我们看一下怎样自定义缓存key。

#### 自定义缓存

@Cacheable 和 @CachePut都有一个名为key属性，这个属性能够替换默认的key，它是通过一个SpEL表达式计算得到的。任意的SpEL表达式都是可行的，但是更常见的场景是所定义的表达式与存储在缓存中的值有关，据此计算得到key。

具体到我们这个场景，我们需要将key设置为所保存Spittle 的 ID。以参数形式传递给save() 的 Spittle还没有保存，因此并没有ID。我们只能通过save() 返回的 Spittle 得到 id 属性。

幸好，在为缓存编写SpEL表达式的时候，Spring暴露了一些很有用的元数据。表13.3列出了SpEL中可用的缓存元数据。

表13.3　Spring提供了多个用来定义缓存规则的SpEL扩展

| 表达式            | 描述                                                       |
| ----------------- | ---------------------------------------------------------- |
| #root.args        | 传递给缓存方法的参数，形式为数组                           |
| #root.caches      | 该方法执行时所对应的缓存，形式为数组                       |
| #root.target      | 目标对象                                                   |
| #root.targetClass | 目标对象的类，是 #root.target.class的简写形式              |
| #root.method      | 缓存方法                                                   |
| #root.methodName  | 缓存方法的名字，是 #root.method.name的简写形式             |
| #result           | 方法调用的返回值（不能用在 @Cacheable 注解上）             |
| #Argument         | 任意的方法参数名（如 #argName）或参数索引（如#a0 或 #p0 ） |

对于 save()方法来说，我们需要的键是所返回Spittle 对象的 id属性。表达式#result能够得到返回的Spittle。借助这个对象，我们可以通过将key属性设置为#result.id 来引用 id 属性：

```java
@CachePut(value="spittleCache", key="#result.id")
Spittle save(Spittle spittle);
```

按照这种方式配置@CachePut，缓存不会去干涉save()方法的执行，但是返回的Spittle将会保存在缓存中，并且缓存的key与Spittle 的 id属性相同。

#### 条件化缓存

通过为方法添加Spring的缓存注解，Spring就会围绕着这个方法创建一个缓存切面。但是，在有些场景下我们可能希望将缓存功能关闭。

@Cacheable 和 @CachePut提供了两个属性用以实现条件化缓存：unless 和 condition，这两个属性都接受一个SpEL表达式。如果unless属性的SpEL表达式计算结果为true，那么缓存方法返回的数据就不会放到缓存中。与之类似，如果condition属性的SpEL表达式计算结果为false，那么对于这个方法缓存就会被禁用掉。

表面上来看，unless 和 condition属性做的是相同的事情。但是，这里有一点细微的差别。unless属性只能阻止将对象放进缓存，但是在这个方法调用的时候，依然会去缓存中进行查找，如果找到了匹配的值，就会返回找到的值。与之不同，如果condition的表达式计算结果为false，那么在这个方法调用的过程中，缓存是被禁用的。就是说，不会去缓存进行查找，同时返回值也不会放进缓存中。

作为样例（尽管有些牵强），假设对于message属性包含“NoCache”的Spittle对象，我们不想对其进行缓存。为了阻止这样的Spittle对象被缓存起来，可以这样设置unless 属性：

```java
@Cacheable(value="spittleCache"
    unless="#result.message.contains('NoCache')")
Spittle findOne(long id);
```

为 unless设置的SpEL表达式会检查返回的Spittle对象（在表达式中通过#result来识别）的message属性。如果它包含“NoCache”文本内容，那么这个表达式的计算值为true ，这个 Spittle对象不会放进缓存中。否则的话，表达式的计算结果为false，无法满足unless的条件，这个Spittle对象会被缓存。

属性 unless能够阻止将值写入到缓存中，但是有时候我们希望将缓存全部禁用。也就是说，在一定的条件下，我们既不希望将值添加到缓存中，也不希望从缓存中获取数据。

例如，对于ID值小于10的Spittle对象，我们不希望对其使用缓存。在这种场景下，这些Spittle是用来进行调试的测试条目，对其进行缓存并没有实际的价值。为了要对ID小于10的Spittle关闭缓存，可以在@Cacheable 上使用 condition属性，如下所示：

```java
@Cacheable(value="spittleCache"
    unless="#result.message.contains('NoCache')"
    condition="#id >= 10")
Spittle findOne(long id);
```

如果 findOne()调用时，参数值小于10，那么将不会在缓存中进行查找，返回的Spittle也不会放进缓存中，就像这个方法没有添加@Cacheable注解一样。

### 移除缓存条目

@CacheEvict并不会往缓存中添加任何东西。相反，如果带有@CacheEvict注解的方法被调用的话，那么会有一个或更多的条目会在缓存中移除。

那么在什么场景下需要从缓存中移除内容呢？当缓存值不再合法时，我们应该确保将其从缓存中移除，这样的话，后续的缓存命中就不会返回旧的或者已经不存在的值，其中一个这样的场景就是数据被删除掉了。这样的话，SpittleRepository 的 remove()方法就是使用@CacheEvict的绝佳选择：

```java
@CacheEvict("spittleCache")
void remove(long spittleId);
```

> 与 @Cacheable 和 @CachePut 不同， @CacheEvict能够应用在返回值为void的方法上，而@Cacheable 和 @CachePut 需要非 void的返回值，它将会作为放在缓存中的条目。因为@CacheEvict只是将条目从缓存中移除，因此它可以放在任意的方法上，甚至void 方法。
>

表13.4　@CacheEvict注解的属性，指定了哪些缓存条目应该被移除掉

| 属　　性         | 类　　型  | 描　　述                                                     |
| ---------------- | --------- | ------------------------------------------------------------ |
| value            | String [] | 要使用的缓存名称                                             |
| key              | String    | SpEL表达式，用来计算自定义的缓存key                          |
| condition        | String    | SpEL表达式，如果得到的值是false的话，缓存不会应用到方法调用上 |
| allEntries       | boolean   | 如果为true的话，特定缓存的所有条目都会被移除掉               |
| beforeInvocation | boolean   | 如果为true的话，在方法调用之前移除条目。如果为 false（默认值）的话，在方法成功调用之后再移除条目 |

## 使用XML声明缓存

你可能想要知道为什么想要以XML的方式声明缓存。毕竟，本章中我们所看到的缓存注解要优雅得多。

我认为有两个原因：

- 你可能会觉得在自己的源码中添加Spring的注解有点不太舒服； 
- 你需要在没有源码的bean上应用缓存功能。

在上面的任意一种情况下，最好（或者说需要）将缓存配置与缓存数据的代码分隔开来。Spring的cache命名空间提供了使用XML声明缓存规则的方法，可以作为面向注解缓存的替代方案。因为缓存是一种面向切面的行为，所以cache命名空间会与Spring的aop命名空间结合起来使用，用来声明缓存所应用的切点在哪里。

要开始配置XML声明的缓存，首先需要创建Spring配置文件，这个文件中要包含cache 和 aop命名空间：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:cache="http://www.springframework.org/schema/cache"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xsi:schemaLocation="http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache.xsd">

  <!-- Caching configuration will go here -->

</beans>
```

cache命名空间定义了在Spring XML配置文件中声明缓存的配置元素。表13.5列出了cache命名空间所提供的所有元素。

| 元素                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| \<cache:annotation-driven> | 启用注解驱动的缓存。等同于Java配置中的 @EnableCaching        |
| \<cache:advice>            | 定义缓存通知（advice）。结合 \<aop:advisor>，将通知应用到切点上 |
| \<cache:caching>           | 在缓存通知中，定义一组特定的缓存规则                         |
| \<cache:cacheable>         | 指明某个方法要进行缓存。等同于 @Cacheable 注解               |
| \<cache:cache-put>         | 指明某个方法要填充缓存，但不会考虑缓存中是否已有匹配的值。等同于 @CachePut 注解 |
| \<cache:cache-evict>       | 指明某个方法要从缓存中移除一个或多个条目，等同于 @CacheEvict 注解 |

\<cache:annotation-driven>元素与Java配置中所对应的@EnableCaching非常类似，会启用注解驱动的缓存。我们已经讨论过这种风格的缓存，因此没有必要再对其进行介绍。

表13.5中其他的元素都用于基于XML的缓存配置。接下来的代码清单展现了如何使用这些元素为SpittleRepositorybean配置缓存，其作用等同于本章前面章使用缓存注解的方式。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:cache="http://www.springframework.org/schema/cache"
  xmlns:aop="http://www.springframework.org/schema/aop"
  xsi:schemaLocation="http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/cache
    http://www.springframework.org/schema/cache/spring-cache.xsd">

  <aop:config>	
    <aop:advisor advice-ref="cacheAdvice"
        pointcut=
          "execution(* com.habuma.spittr.db.SpittleRepository.*(..))"/>
  </aop:config>

  <cache:advice id="cacheAdvice">
    <cache:caching>
      <cache:cacheable	
          cache="spittleCache"
          method="findRecent" />

      <cache:cacheable	
           cache="spittleCache"         method="findOne" />
      <cache:cacheable	
          cache="spittleCache"
          method="findBySpitterId" />

      <cache:cache-put	
          cache="spittleCache"
          method="save"
          key="#result.id" />

      <cache:cache-evict	
          cache="spittleCache"
          method="remove" />

    </cache:caching>
  </cache:advice>

  <bean id="cacheManager" class=
      "org.springframework.cache.concurrent.ConcurrentMapCacheManager"
  />

</beans>
```

在程序清单13.7中，我们首先看到的是\<aop:advisor>，它引用ID为cacheAdvice的通知，该元素将这个通知与一个切点进行匹配，因此建立了一个完整的切面。在本例中，这个切面的切点会在执行SpittleRepository的任意方法时触发。如果这样的方法被Spring应用上下文中的任意某个bean所调用，那么就会调用切面的通知。

在这里，通知利用\<cache:advice>元素进行了声明。在\<cache:advice>元素中，可以包含任意数量的\<cache:caching>元素，这些元素用来完整地定义应用的缓存规则。在本例中，只包含了一个\<cache:caching>元素。这个元素又包含了三个\<cache:cacheable>元素和一个\<cache:cache-put> 元素。

每个 \<cache:cacheable>元素都声明了切点中的某一个方法是支持缓存的。这是与@Cacheable注解同等作用的XML元素。具体来讲，findRecent() 、 findOne() 和 findBySpitterId()都声明为支持缓存，它们的返回值将会保存在名为spittleCache的缓存之中。

\<cache:cache-put>是Spring XML中与@CachePut注解同等作用的元素。它表明一个方法的返回值要填充到缓存之中，但是这个方法本身并不会从缓存中获取返回值。在本例中，save()方法用来填充缓存。同面向注解的缓存一样，我们需要将默认的key改为返回Spittle 对象的 id 属性。

最后， \<cache:cache-evict>元素是Spring XML中用来替代@CacheEvict注解的。它会从缓存中移除元素，这样的话，下次有人进行查找的时候就找不到了。在这里，调用remove()时，会将缓存中的Spittle删除掉，其中key与remove()方法所传递进来的ID参数相等的条目会从缓存中移除。

需要注意的是，\<cache:advice>元素有一个cache-manager元素，用来指定作为缓存管理器的bean。它的默认值是cacheManager，这与程序清单13.7底部的<bean>声明恰好是一致的，所以没有必要再显式地进行设置。但是，如果缓存管理器的ID与之不同的话（使用多个缓存管理器的时候，可能会遇到这样的场景），那么可以通过设置cache-manager属性指定要使用哪个缓存管理器。

需要注意的是，\<cache:advice>元素有一个cache-manager元素，用来指定作为缓存管理器的bean。它的默认值是cacheManager，这与程序清单13.7底部的<bean>声明恰好是一致的，所以没有必要再显式地进行设置。但是，如果缓存管理器的ID与之不同的话（使用多个缓存管理器的时候，可能会遇到这样的场景），那么可以通过设置cache-manager属性指定要使用哪个缓存管理器。

另外，还要留意的是，\<cache:cacheable> 、 \<cache:cache-put> 和 \<cache:cache-evict>元素都引用了同一个名为spittleCache的缓存。为了消除这种重复，我们可以在\<cache:caching>元素上指明缓存的名字：

```xml
<cache:advice id="cacheAdvice">
  <cache:caching cache="spittleCache">

    <cache:cacheable method="findRecent" />

    <cache:cacheable method="findOne" />

    <cache:cacheable method="findBySpitterId" />

    <cache:cache-put
        method="save"
        key="#result.id" />

    <cache:cache-evict method="remove" />

  </cache:caching>
</cache:advice>
```

\<cache:caching>有几个可以供\<cache:cacheable> 、 \<cache:cache-put> 和 \<cache:cache-evict>共享的属性，包括： 

- cache：指明要存储和获取值的缓存； 
- condition：SpEL表达式，如果计算得到的值为false，将会为这个方法禁用缓存； 
- key：SpEL表达式，用来得到缓存的key（默认为方法的参数）； 
- method：要缓存的方法名。

除此之外，\<cache:cacheable> 和 \<cache:cache-put> 还有一个 unless属性，可以为这个可选的属性指定一个SpEL表达式，如果这个表达式的计算结果为true，那么将会阻止将返回值放到缓存之中。

\<cache:cache-evict>元素还有几个特有的属性： 

- all-entries ：如果是 true的话，缓存中所有的条目都会被移除掉。如果是false的话，只有匹配key的条目才会被移除掉。 
- before-invocation ：如果是 true的话，缓存条目将会在方法调用之前被移除掉。如果是false的话，方法调用之后才会移除缓存。 

all-entries 和 before-invocation的默认值都是false。这意味着在使用\<cache:cache-evict>元素且不配置这两个属性时，会在方法调用完成后只删除一个缓存条目。要删除的条目会通过默认的key（基于方法的参数）进行识别，当然也可以通过为名为key的属性设置一个SpEL表达式指定要删除的key。

 

