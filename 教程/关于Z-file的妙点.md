1.返回值都用一个returnbean来包装，returnBean中包含返回码和反馈信息，如果有后台数据，不管是什么数据，都放在一个类中，到了前台再处理。

2.使用事务，让数据保持一致、安全。

3.对于模糊查询等条件查询，直接调用底层的example类，进行封装，对数据库数据直接进行比对查询

4.Optional类用来代替null的类

5.复制对象属性值到另一个对象，可用BeanUtils.copyProperties()

6.缓存使用hutool来构造

