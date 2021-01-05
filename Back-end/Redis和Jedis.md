## #前言

想通过缓存来保存微信公众号的`accesstoken`和`refreshtoken`，自己写也可以，就是在保存在某个类中，每次启动项目，调用这个类就可以实现存储，针对`accesstoken`生成一个，可用于所有用户和各个接口的调用的，还比较容易，但是`refreshtoken`需要匹配每个用户，怎么针对用户id来存储，就是一个难题了，虽然我之前使用`list`也可以实现，但是说实话，有点土！:unamused:

## Redis


### 命令

进入redis：cd path/to/redis-6.0.6/src

> 要进入到src目录才能看到redis.server和redis.client

启动redis服务器：./redis-server &

启动redis客户端：./redis-cli

> 启动服务器才能用客户端来执行增删改查相关命令语句

获取缓存：get (key)

设置缓存：set (key) (value)

获取hash缓存：hget (key) (field)

删除所有缓存：flushdb

<hr>

## Java Operation Object--jedis

### 配置

> 注意版本spring-data-redis版本！

```xml
#在pom.xml上引入依赖，我是使用ssm框架来搭建的
<!-- 配置redis -->
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.8.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
</dependency>
```

在spring容器中配置两个关键的类`JedisConnectionFactory`和`RedisTemplate/StringRedisTemplate`。

这样就可以直接利用`RedisTemplate`对象来操作了。

当然，我推荐使用`StringRedisTemplate`,操作更加方便！:laughing:

<hr>

### 操作

#### RedisTemplate注解 操作对象

可以直接使用，类似于使用`RedisTemplate`调用`OpsForXxx`

```java
//Redis geospatial operations, such as GEOADD, GEORADIUS
GeoOperations

//Redis hash operations
HashOperations

//Redis HyperLogLog operations, such as PFADD, PFCOUNT
HyperLogLogOperations

//Redis list operations
ListOperations

//Redis set operations
SetOperations

//Redis string (or value) operations
ValueOperations

//Redis zset (or sorted set) operations
ZSetOperations
```

<hr>

#### 特殊的方法

```java
//通过opsForValue()也可执行redisTemplate的方法。
redisTemplate.opsForValue().getOperations().getExpire("key的名称")
```

<hr>

#### StringRedisTemplate操作集锦

```java
*//向redis里存入数据和设置缓存时间*  
stringRedisTemplate.opsForValue().set("baike", "100", 60 * 10, TimeUnit.SECONDS);

*//val做-1操作*  
stringRedisTemplate.boundValueOps("baike").increment(-1);

*//根据key获取缓存中的val*  
stringRedisTemplate.opsForValue().get("baike")

*//val +1*  
stringRedisTemplate.boundValueOps("baike").increment(1);

*//根据key获取过期时间*  
stringRedisTemplate.getExpire("baike");

*//根据key获取过期时间并换算成指定单位*  
stringRedisTemplate.getExpire("baike",TimeUnit.SECONDS);

*//根据key删除缓存*  
stringRedisTemplate.delete("baike");

//检查key是否存在，返回boolean值*  
stringRedisTemplate.hasKey("baike");

*//向指定key中存放set集合*  
stringRedisTemplate.opsForSet().add("baike", "1","2","3");

//设置过期时间*  
stringRedisTemplate.expire("baike",1000 , TimeUnit.MILLISECONDS);

//根据key查看集合中是否存在指定数据*  
stringRedisTemplate.opsForSet().isMember("baike", "1");

//根据key获取set集合*  
stringRedisTemplate.opsForSet().members("baike");

//验证有效时间*
Long expire = redisTemplate.boundHashOps("baike").getExpire();

//使用底层命令
StringRedisTemplate.execute((RedisCallback<Void>) con -> { con.hSet("age".getBytes(),"23".getBytes());
return null;    })
```

<hr>

#### RedisTemplate常用集合使用说明-opsForHash

```java
1、put(H key, HK hashKey, HV value) 
    //新增hashMap值 
    redisTemplate.opsForHash().put("hashValue","map1","map1-1");   		
	redisTemplate.opsForHash().put("hashValue","map2","map2-2");    

2、values(H key) 
    //获取指定变量中的hashMap值。
    List<Object> hashList = redisTemplate.opsForHash().values("hashValue");   
	System.out.println("通过values(H key)方法获取变量中的hashMap值:" + hashList);   

3、entries(H key) 
    //获取变量中的键值对。 
	Map<Object,Object> map = redisTemplate.opsForHash().entries("hashValue");   
	System.out.println("通过entries(H key)方法获取变量中的键值对:" + map);  

4、get(H key, Object hashKey) 
   // 获取变量中的指定map键是否有值,如果存在该map键则获取值，没有则返回null。 
	Object mapValue = redisTemplate.opsForHash().get("hashValue","map1");   	
	System.out.println("通过get(H key, Object hashKey)方法获取map键的值:" + mapValue); 

5、//hasKey(H key, Object hashKey) 
    判断变量中是否有指定的map键。 
    boolean hashKeyBoolean = redisTemplate.opsForHash().hasKey("hashValue","map3"); 
	System.out.println("通过hasKey(H key, Object hashKey)方法判断变量中是否存在map键:" + hashKeyBoolean);     

6、keys(H key) 
   // 获取变量中的键。 
	Set<Object> keySet = redisTemplate.opsForHash().keys("hashValue");   
	System.out.println("通过keys(H key)方法获取变量中的键:" + keySet);    

7、size(H key) 
   // 获取变量的长度。 
    long hashLength = redisTemplate.opsForHash().size("hashValue");   
	System.out.println("通过size(H key)方法获取变量的长度:" + hashLength);    

8、increment(H key, HK hashKey, double delta)  
   // 使变量中的键以double值的大小进行自增长。
    double hashIncDouble = 						redisTemplate.opsForHash().increment("hashInc","map1",3);   
	System.out.println("通过increment(H key, HK hashKey, double delta)方法使变量中的键以值的大小进行自增长:" + hashIncDouble);    

9、increment(H key, HK hashKey, long delta) 
    //使变量中的键以long值的大小进行自增长。 
    long hashIncLong = redisTemplate.opsForHash().increment("hashInc","map2",6);   	
	System.out.println("通过increment(H key, HK hashKey, long delta)方法使变量中的键以值的大小进行自增长:" + hashIncLong);    

10、multiGet(H key, Collection<HK> hashKeys) 
    //以集合的方式获取变量中的值。 
    List<Object> list = new ArrayList<Object>();   
	list.add("map1");   
	list.add("map2");   
	List mapValueList = redisTemplate.opsForHash().multiGet("hashValue",list);  
	System.out.println("通过multiGet(H key, Collection<HK> hashKeys)方法以集合的方式获取变量中的值:"+mapValueList);    

11、putAll(H key, Map<? extends HK,? extends HV> m)  
    //以map集合的形式添加键值对。 
    Map newMap = new HashMap();   
	newMap.put("map3","map3-3");   
	newMap.put("map5","map5-5");   	
	redisTemplate.opsForHash().putAll("hashValue",newMap);   
	map = redisTemplate.opsForHash().entries("hashValue");   
	System.out.println("通过putAll(H key, Map<? extends HK,? extends HV> m)方法以map集合的形式添加键值对:" + map);    

12、putIfAbsent(H key, HK hashKey, HV value) 
    //如果变量值存在，在变量中可以添加不存在的的键值对，如果变量不存在，则新增一个变量，同时将键值对添加到该变量。 
    redisTemplate.opsForHash().putIfAbsent("hashValue","map6","map6-6");   
	map = redisTemplate.opsForHash().entries("hashValue");   
	System.out.println("通过putIfAbsent(H key, HK hashKey, HV value)方法添加不存在于变量中的键值对:" + map);    

13、scan(H key, ScanOptions options) 
	//匹配获取键值对，ScanOptions.NONE为获取全部键对
    ScanOptions.scanOptions().match("map1").build()     
    //匹配获取键位map1的键值对,不能模糊匹配。 
    Cursor<Map.Entry<Object,Object>> cursor = redisTemplate.opsForHash().scan("hashValue",ScanOptions.scanOptions().match("map1").build());  
*//Cursor> cursor = redisTemplate.opsForHash().scan("hashValue",ScanOptions.NONE);*   
while (cursor.hasNext()){    
    Map.Entry<Object,Object> entry = cursor.next();    
    System.out.println("通过scan(H key, ScanOptions options)方法获取匹配键值对:" + entry.getKey() + "---->" + entry.getValue());  
}    

14、delete(H key, Object... hashKeys) 
    //删除变量中的键值对，可以传入多个参数，删除多个键值对。 	
    redisTemplate.opsForHash().delete("hashValue","map1","map2");   
	map = redisTemplate.opsForHash().entries("hashValue");   
	System.out.println("通过delete(H key, Object... hashKeys)方法删除变量中的键值对后剩余的:" + map); 

```

> 头晕不:dizzy:，慢慢看吧:eyes:,多刷几遍，就懂了:grin:

<hr>

#### Hash mapping

```java
//实现object与Map<K,V>相互转换
//法1.ObjectHashMapper
这个对象返回的是字节类型的Hashmap，所以还是使用Jackson2HashMapper吧

//法2.Jackson2HashMapper
HashMapper<Object,String,Object> hashMapper = new Jackson2HashMapper(false);
Map<String, Object> hash = hashMapper.toHash(user);
```

