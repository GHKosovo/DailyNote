# 反射机制

## 反射机制有什么用？

通过Java语言中的反射机制可以操作字节码文件。有点类似黑客，可以都和写字节码文件；通过反射机制可以操作代码片段（class文件）

大多数框架都是使用反射机制，比如Hibernate,Spring等等

**反射机制的相关类在哪个包下？**

java.lang.reflect.*;

**反射机制相关的重要的类有哪些？**

java.lang.Class：代表整个字节码文件；代表一个类型，代表整个类

java.lang.reflect.Method：代表字节码中的方法字节码；代表类中的方法

java.lang.reflect.Constructor：代表字节码中的构造方法字节码；代表类中的构造方法

java.lang.reflect.Field：代表字节码中的属性字节码；代表类中的成员变量（静态变量+实例变量）

## 获取字节码的方式

要操作一个类的字节码，首先需要获取到这个类的字节码，获取java.lang.Class实例的三种方式：

- 第一种：Class c = Class.forName("完整类带包名")；
- 第二种：Class c = 对象.getClass()；
- 第三种：Class c = 任何类型,class；

## 反射机制的优点

反射机制非常灵活，java代码写一遍，再不改变java源代码的基础之上，可以做到不同对象的实例化，非常灵活；符合OCP开闭原则：对外扩展开放，对内修改关闭。

## 相关

### Class.forName发生了什么？

如果只是希望一个类的静态代码块执行，其他代码一律不执行，可以使用：

`Class.forName()`，这个方法的执行会导致类加载，类加载时，静态代码块执行。

### 获取类路径下的文件的绝对路径

凡是在src下的都是在类路径下，src是类的根路径

**怎么获取一个文件的绝对路径？**以下的方式是通用的，但是前提是文件需要在类路径下

```java
//Thread.currentThread() 当前对象
//getContextClassLoader() 是线程对象的方法，可以获取当前线程的类加载器对象
//getResource() 【获取资源】这是类加载器对象的方法，当前线程的类加载器默认从类的根路径下加载资源。
String path = Thread.currentThread().getContextClassLoader().getResource().getPath();
```

**绝对路径的另一种使用方式**

直接返回文件流。

```java
//直接以流的形式返回
InputStream reader = Thread.currentThread().getContextClassLoader().getResourceStream("classinfo2.properties");
Properties pro = new Properties();
pro.load(reader);
reader.close()
//通过key获取value
String className = pro.getProperty("className");
System.out.println(className);
```

### 资源绑定器

java.util包下提供了一个资源绑定器，便于获取属性配置文件中的内容。

使用这种方式的时候，属性配置文件必须放到类路径下

**资源绑定器**，只能绑定xxx.properties文件，也就是文件扩展名必须是properties；而且这个文件必须在类路径下；

注意：在写路径的时候，路径后面的扩展名不能写。

```java
//ResourceBundle bundle = ResourceBundle.getBundle("classinfo2");
ResourceBundle bundle = ResourceBundle.getBundle("classinfo2");
String className = pro.getProperty("com/lj/java/bean/className");
System.out.println(className);
```

