## 概述

使用事务管理在项目中非常重要，当技术不管进步，设置事务变得越来越简单

## 配置事务

Spring3.1可以通过**@EnableTransactionManagement**在*@Configuration*配置类中开启事务支持

```java
@Configuration
@EnableTransactionManagement
public class PersistenceJPAConfig{

   @Bean
   public LocalContainerEntityManagerFactoryBean
     entityManagerFactoryBean(){
      //...
   }

   @Bean
   public PlatformTransactionManager transactionManager(){
      JpaTransactionManager transactionManager
        = new JpaTransactionManager();
      transactionManager.setEntityManagerFactory(
        entityManagerFactoryBean().getObject() );
      return transactionManager;
   }
}
```

然而，在Spring Boot 项目中，如果有*spring-data-\**或者*spring-tx*依赖，那么事务管理会默认自动开启

## 使用XML配置事务

史前的版本（spring3.1以前），是用注解驱动和命名空间支持的XM配置

```xml
<bean id="txManager" class="org.springframework.orm.jpa.JpaTransactionManager">
   <property name="entityManagerFactory" ref="myEmf" />
</bean>
<tx:annotation-driven transaction-manager="txManager" />
```

## @Transactional注解

```java
@Service
@Transactional
public class FooService {
    //...
}
```

#详细的说明文档在[这里](https://www.baeldung.com/transaction-configuration-with-jpa-and-spring)

