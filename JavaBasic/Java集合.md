## 集合的知识点

![img](https://img2018.cnblogs.com/i-beta/1681961/202001/1681961-20200112133520292-730123310.png)

Java集合类提供了一套设计良好的支持对一组对象进行操作的接口和类。Java集合类里面最基本的接口有：

**Collection**：代表一组对象，每一个对象都是它的子元素。

**Set**：不包含重复元素，无序的Collection。

**List**：有顺序的collection，并且可以包含重复元素。

**Map**：可以把键(key)映射到值(value)的对象，键不能重复。

不论Collection的实际类型如何，它都支持一个iterator（）的方法;一般都是创建子类对象后赋值给父类，这样可以使用更多的方法。

Java集合类库将集合的接口与实现分离。同样的接口，可以有不同的实现。Java集合类的基本接口是Collection接口。而Collection接口必须继承java.lang.Iterable接口。

> 关于集合的介绍详情在[这里](https://www.cnblogs.com/jmsjh/p/7740123.html)

## Map集合

对于map,都知道是键值对，但是跟数组有啥区别？map类型可理解为关联数组，可使用键值对作为下标来获取一个值，正如内置数组类型一样。而关联的本质是元素的值与某个特定键相关联，而并非通过元素在数组中的位置来获取。

#### 方法

Map集合方法有：equals(Object o)(比较指定对象与此map的等价性)、hashCode()（返回此map的哈希码）、clear()（从map中删除所有映射）、remove(Object key)（从Map中删除键和关联的值）、put(Object key,Object value)（将指定值与指定键相关联）、putAll(Map t)（将指定Map中的所有映射复制到此map），其中hashMap还有Get()方法

#### Hashtable&HashMap&ConcurrentHashMap之间的区别

Hashtable是线程安全对象，会出现阻塞，效率不高；hashtable提供了对键的列举(Enumeration);Hashtable不允许键或者值为null

> Enumeration有点类似于迭代器，hasMoreElements()方法用于判断是否还有子元素；而nextElement()方法则是获取下一个元素。

HashMap是线程不安全对象，不会出现阻塞现象，效率高；但是多线程情况下，可能会出现线程不安全的情况，甚至出现死锁;

HashMap提供了迭代器；允许键和值为null

concurrentHashMap兼顾前面两者；

> HashMap的底层原理：HashMap的数据结构是数组和链表的结合；HashMap需要一个Hash函数，用它来生成hash值，这个值就是这个hashmap元素的存储地址，到目前为止都是使用数组来存储，但是hash值有可能相等，这就会发生哈希冲突，这时候就使用链表在同一个位置插入，所以这时候就是使用链表存储。
>
> 所以我们一般使用hashCode()和equals()方法来向集合/从集合添加和检索元素。
>
> hashCode()返回hash值，如果hash值相同再使用equals()方法来比较key值是否相同，以此来确定元素。

## Iterator(迭代器)

对于迭代器Iterator，它是一种设计模式，是一个对象，它可以遍历并选择序列中的对象可用，集合类可使用方法iterator（）来生成一个迭代器对象

#### 方法

迭代器方法有next()(获得序列中的下一个元素)、hasNext()（检查序列中是否还有元素）、remove()（删除序列中的元素）；

## List集合

List接口主要有两个子类LinkedList和ArrayList，当然还有比较少用的vector类和stack类

#### Arraylist和Linkedlist的区别？

```
Arraylist是数组，可以自增的数组，每次自增为1.5倍，自增的方式是位运算，它在内存地址中是连续内存空间；而linkedlist是链表，双向链表，它是采用前后节点指针的方式串联起来；
```

#### 方法

常用方法有：add(Object e )向集合末尾处添加指定的元素、remove(Object e)将指定元素对象，从集合删除，返回值为被删除的对象、set(int index,Object e)将指定索引处的元素，替换成指定的元素，返回值为替换前的元素、get(int index)获取指定索引处的元素，并返回该元素。

- 对于**LindedList**，LinkedList实现了List[接口](http://baike.baidu.com/view/159864.htm)，允许null元素。此外LinkedList提供额外的get，remove，insert方法在LinkedList的首部或尾部。这些操作使LinkedList可被用作堆栈（stack），队列（queue）或双向队列（deque）。

- 对于**ArrayList**，ArrayList实现了可变大小的[数组](http://baike.baidu.com/view/209670.htm)。它允许所有元素，包括null。ArrayList没有同步(如果多个线程同时访问一个List，则必须自己实现访问同步,LinkedList也是不同步的)。size，isEmpty，get，set方法运行时间为常数。

- 对于**Vector**, Vector非常类似ArrayList，但是Vector是同步的，也就是说Vector是用synchronized(上锁)修饰的类，所以它是线程安全的。由Vector创建的Iterator，虽然和ArrayList创建的Iterator是同一[接口](http://baike.baidu.com/view/159864.htm)，但是，因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此必须捕获该异常。

- 对于**Stack**，Stack继承自Vector，实现一个后进先出的[堆栈](http://baike.baidu.com/view/93201.htm)。Stack提供5个额外的方法使得Vector得以被当作[堆栈](http://baike.baidu.com/view/93201.htm)使用。基本的push和pop方法，还有peek方法得到栈顶的元素，empty方法测试[堆栈](http://baike.baidu.com/view/93201.htm)是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈。

## Set集合

对于Set接口。Set是一种不包含重复的元素的Collection，即任意的两个元素e1和e2都有e1.equals(e2）=false，Set最多有一个null元素。

set有两种常用的实现类HashSet和TreeSet

> set的详细介绍在[这里](https://www.jianshu.com/p/b48c47a42916)