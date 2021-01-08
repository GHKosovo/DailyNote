## Comparable

这是一个内比较器，实现了这个接口的类，这些类就可以和自己作比较，如果要和实现了这个接口的另一个类作比较，则需要使用compareTo方法。这个方法可以个给两个对象排序。具体来说，它返回负数，0，正数来表明已经存在的对象小于，等于，大于输入对象。

如果对象被放到集合中去，尔后需要使用集合的sort方法来排序，那么这些对象就必须实现Comparable接口；

## Comparator

这是个外比较器，如果对象要跟自己比较而没有实现Comparable接口或不想要使用Comparable接口中的compareTo接口，则可以使用comparator的方法comparetor，这个方法有两个参数，方法返回值跟Comparable一样；

具体案例可参考[这里](https://www.jianshu.com/p/f9870fd05958)