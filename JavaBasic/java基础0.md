### 1、能直接新建一个接口？

当然不行，但是我明明看过，比如以下代码

```
Collections.sort(list,new Comparator<Student>(){
	@Override
	public int Compara(Student s1,Student s2){
		if (s1.age > s1.age()) {
            return 1;
        }
        if (s1.age < s2.age()) {
            return -1;
        }
        return 0;
	}
})
```

实际上，这是new 了一个实现了结构的**匿名类**，需要在匿名类内部实现那个接口



### 2、Class对象和ClassLoader

#### ClassLoader做了什么？

其实ClassLoader就是用啦加载Class的，把编译后的字节码文件*.class转换为内存中的Class对象。每个Class对象内部都有一个classLoader字段来标识自己是由哪个ClassLoader加载的。ClassLoader就像一个容器，里面装载了许多已经加载的Class对象。

#### 延迟加载

JVM运行，并不是一次性加载所需要的全部类，而是按需加载。程序在运行的过程中会慢慢遇到很多不认识的新类，这时候就会调用ClassLoader来加载这些类，加载完成后就会将Class对象存在ClassLoader里面，下次就不需要重新加载了。

#### 多个ClassLoader

JVM 运行实例中会存在多个 ClassLoader，不同的 ClassLoader 会从不同的地方加载字节码文件。它可以从不同的文件目录加载，也可以从不同的 jar 文件中加载，也可以从网络上不同的服务地址来加载。JVM 中内置了三个重要的 ClassLoader，分别是 BootstrapClassLoader、ExtensionClassLoader 和 AppClassLoader。

#### ClassLoader的传递性

在程序运行期间，如果遇到一个未知的类，程序会选择哪个ClassLoader来加载它呢？虚拟机的策略是使用调用者Class对象的ClassLoader来加载当前的未知类。何为调用者Class对象？就是在遇到未知类时，虚拟机肯定正在运行一个方法的调用（静态方法或者实例方法），这个方法挂在哪个类上面，那这个类就是调用者Class对象。

之前提到每个Class对象里面都有一个classLoader属性记录了当前的类是由谁来加载的，由于ClassLoader的传递性，所有延迟加载的类都会有初始调用main方法的这个ClassLoader全权负责，它就是AppClassLoader。

#### 双亲委派

其实在调用ClassLoader加载工作的时候，一般都是AppClassLoader负责，但是AppClassLoader在加载之前会调用他的parent，即BootstrapClassLoader 和 ExtensionClassLoader 来加载，如果他们都没法加载，最后才是AppLoader来加载

#### Class.forName

这个东西在使用jdbc驱动时，经常会用到；

forName方法其实也是使用调用者Class对象的ClassLoader来加载目标类。

#### 如何获取ClassLoader

有四种方法来获取加载器：

1. 通过当前线程获取：Thread.currentThread().getContextClassLoader()

2. java自身的class：ClassName.class.getClassLoader()

3. 已知执行类的类加载器：getClass().getClassLoader()
4. 工具类的类加载器： ClassLoaderUtil.class.getClassLoader()

**关于更多关于ClassLoader的说明，[请看这里](https://zhuanlan.zhihu.com/p/51374915)**

#### Class对象是什么？

在阐述Class对象之前，我们先来了解Class类，提到Class类，我们就从最初的类开始阐述吧；

类是一类事物的描述，类包含了这类事物的信息。用属性和行为可以描述一类事物。那么“类”这个事物是否也可以被描述呢？当然啦，Class类就是用来描述类的信息的。Class也是一种类型，他专门用来描述类的特征。比如车这个类可以用名称，类型，行为来描述。类这个类可以用属性，行为来描述，所有的类都可以有属性，都可以有行为。所以Class类其实是类的更高层抽象。

Class对象其实就是普通类类型，比如车这个类

#### Class对象是怎么来？

程序运行的时候，编译好的字节码文件*.class会被加载到jvm种，jvm会调用ClassLoader来加载这个类，转换为内存中的Class对象，当第一次加载这个类的时候，JVM就会使用这个类的Class对象，也即是刚刚生成的那个，来获取该类的信息，然后创建对象，也就是实例对象。每个类对应的这个Class对象只有一个。

![img](https://pic3.zhimg.com/80/v2-0d24cf12eb6a76b71362f147f5560d5a_720w.jpg)

> 获取Class对象信息的时候是**运行时**，只有在**运行时**才能通过Class获取类的信息。

#### 如何获取Class对象？

方法有四种：

1. Class.forName("类名"); 通过类名字符串获取Class对象。
2. 通过类的对象调用getClass() 获取该类型的Class对象
3. 通过类型直接获取Class对象
4. 通过类加载器`xxxClassLoader.loadClass()`传入类路径获取

使用Class.forName方法可以获取任意类对象

而使用getClass()方法则只能用于获取当前对象所属的Class对象 

#### 如何使用Class对象？

Class对象有类里面所有的信息，所以可以通过Class对象那个获取类里面所有的构造器，方法，成员变量等等所有信息。

其实Class对象一般用于**反射机制**。了解Class对象对了解反射机制非常种要。

getClassLoader()：获取Class对象的类装载器(类加载器)

类装载器负责将类名的字节流/字符流读入内存，并构造Class对象，而实例对象就是通过Class对象来创建的。

