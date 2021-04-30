jpa的特点：

1.没有sql语句；需要写一个bean:EntityManagerFactory，用于调用Repository的接口方法；EntityManagerFactory可以用Springboot自动配置生成。

2.跟mybatis一样（这边都是以Repository来代替dao）只要写Repository接口层就可以了，不需要写它的实现类

3.实体类需要@Entity注解和和@Id注解

4.Repository接口实现类需要继承JPARepository<T,T>就可以自动实现18个操作方法了



