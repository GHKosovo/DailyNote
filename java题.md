1. 类域或类方法：意思就是属于一个类的字段和方法；而不是仅仅需于哪个实例；故，类域即静态成员；类方法即静态方法；

2. 构造函数的名字与类的名字相同，而且不能指定返回类型

3. static 修饰符 和 final 修饰符

4. String类的方法：

   - substring(start,stop)  返回子串，不包括stop这个点

   - charAt() 返回指定索引处的字符

5. Calendar类

   - 可以直接调用Calendar.getInstance()方法获取Calendar实例
   - 可通过setTime（）方法设置它的日期，前提是内容必须为Date形式
   - getTime（）方法返回日期
   - set()方法用于将给定日历字段设置为给定的值，也就是在setTime()设定日历的基础上再设定值
   - get()用于获取在setTime()设定日历的基础上获取值，如果不提前设定值，则默认是当前系统时间的值
   - add()用于在setTime()设定日历的基础上添加或者减去某个日志值

6. SimpleDateFormat类可用来格式化日期，他是DateFormat的子类

   - 新建一个SimpleDateFormat时，需要指定时间格式；
   - 之后使用这个对象实例的parse()方法来格式化数据为Date格式
   - format()方法用于格式化Date日期为String类的时间；

   

Could not run phased build action using Gradle distribution 'https://services.gradle.org/distributions/
 gradle-5.4-bin.zip'. Build file 'C:
 \Users\llj\Downloads\Compressed\SpringiA4_SourceCode\Chapter_13\caching\build.gradle' line: 26 A 
 problem occurred evaluating root project 'caching'. Cannot add task 'wrapper' as a task with that 
 name already exists.