## 概述

Optional可以包含或不包含非null值得容器对象。如果存在值，`isPresent()`将返回true并`get()`返回该值。

其实Optional可以用来代替null值，这样就不用一直去判断返回的对象是否是null，直接调用Optional的方法就可以了

## 方法

**常见方法**

1. empty()：返回一个空Optional实例；
2. get()：如果值存在，返回Optional内部的值，不然抛出`NoSuchElementException`
3. isPresent()：如果值存在返回true;不然返回false;
4. of(T value)：如果值存在则返回一个带有值的Optional对象，不然报[NullPointerException](https://docs.oracle.com/javase/8/docs/api/java/lang/NullPointerException.html)
5. orElse(T other)：如果值存在，返回该值
6. ifPresent(Consumer<? super T> consumer)：如果值存在，就调用特定的consumer;