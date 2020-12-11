## 集合的知识点

1. 不论Collection的实际类型如何，它都支持一个iterator（）的方法;一般都是创建子类对象后赋值给父类，这样可以使用更多的方法。

2. Java集合类库将集合的接口与实现分离。同样的接口，可以有不同的实现。Java集合类的基本接口是Collection接口。而Collection接口必须继承java.lang.Iterable接口。

3. 对于map,都知道是键值对，但是跟数组有啥区别？map类型可理解为关联数组，可使用键值对作为下标来获取一个值，正如内置数组类型一样。而关联的本质是元素的值与某个特定键相关联，而并非通过元素在数组中的位置来获取。

   方法有：equals(Object o)(比较指定对象与此map的等价性)、hashCode()（返回此map的哈希码）、clear()（从map中删除所有映射）、remove(Object key)（从Map中删除键和关联的值）、put(Object key,Object value)（将指定值与指定键相关联）、putAll(Map t)（将指定Map中的所有映射复制到此map），其中hashMap还有Get()方法

4. 对于迭代器Iterator，它是一种设计模式，是一个对象，它可以遍历并选择序列中的对象可用，使用方法iterator（）来生成一个迭代器对象，方法有next()(获得序列中的下一个元素)、hasNext()（检查序列中是否还有元素）、remove()（删除序列中的元素）；

5. List接口有两个子类LinkedList和ArrayList，常用方法有：add(Object e )向集合末尾处添加指定的元素、remove(Object e)将指定元素对象，从集合删除，返回值为被删除的对象、set(int index,Object e)将指定索引处的元素，替换成指定的元素，返回值为替换前的元素、get(int index)获取指定索引处的元素，并返回该元素。

6. 对于LindedList, LinkedList实现了List[接口](http://baike.baidu.com/view/159864.htm)，允许null元素。此外LinkedList提供额外的get，remove，insert方法在LinkedList的首部或尾部。这些操作使LinkedList可被用作堆栈（stack），队列（queue）或双向队列（deque）。

7. 对于ArrayList,ArrayList实现了可变大小的[数组](http://baike.baidu.com/view/209670.htm)。它允许所有元素，包括null。ArrayList没有同步(如果多个线程同时访问一个List，则必须自己实现访问同步,LinkedList也是不同步的)。size，isEmpty，get，set方法运行时间为常数。

8. 对于Vector, Vector非常类似ArrayList，但是Vector是同步的。由Vector创建的Iterator，虽然和ArrayList创建的Iterator是同一[接口](http://baike.baidu.com/view/159864.htm)，但是，因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此必须捕获该异常。

9. 对于Stack，Stack继承自Vector，实现一个后进先出的[堆栈](http://baike.baidu.com/view/93201.htm)。Stack提供5个额外的方法使得Vector得以被当作[堆栈](http://baike.baidu.com/view/93201.htm)使用。基本的push和pop方法，还有peek方法得到栈顶的元素，empty方法测试[堆栈](http://baike.baidu.com/view/93201.htm)是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈。

10. 对于Set接口。Set是一种不包含重复的元素的Collection，即任意的两个元素e1和e2都有e1.equals(e2）=false，Set最多有一个null元素。

> 关于集合的介绍详情在[这里](https://www.cnblogs.com/jmsjh/p/7740123.html)