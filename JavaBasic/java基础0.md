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

其实ClassLoader就是用来加载Class的，把编译后的字节码文件*.class转换为内存中的Class对象。每个Class对象内部都有一个classLoader字段来标识自己是由哪个ClassLoader加载的。ClassLoader就像一个容器，里面装载了许多已经加载的Class对象。

大致步骤 如下：

1. 装载：查找和导入Class文件到JAVA虚拟机中
2. 链接/连接：执行校验、准备和解析步骤，解析步骤可选
   - 校验：检查载入Class文件数据的正确性
   - 准备：给类的静态变量分配存储空间
   - 解析：将符号引用转换成直接引用，如内存地址指针
3. 初始化：对类的静态变量、静态代码块执行初始化工作。

最终结果就是一个被装载类型的Class类的实例对象，，它成为JAVA程序与内部数据结构之间的接口

#### 延迟加载

JVM运行，并不是一次性加载所需要的全部类，而是按需加载。程序在运行的过程中会慢慢遇到很多不认识的新类，这时候就会调用ClassLoader来加载这些类，加载完成后就会将Class对象存在ClassLoader里面，下次就不需要重新加载了。

#### 多个ClassLoader

JVM 运行实例中会存在多个 ClassLoader，不同的 ClassLoader 会从不同的地方加载字节码文件。它可以从不同的文件目录加载，也可以从不同的 jar 文件中加载，也可以从网络上不同的服务地址来加载。JVM 中内置了三个重要的 ClassLoader，分别是 BootstrapClassLoader、ExtensionClassLoader 和 AppClassLoader。

#### ClassLoader的传递性

在程序运行期间，如果遇到一个未知的类，程序会选择哪个ClassLoader来加载它呢？虚拟机的策略是使用调用者Class对象的ClassLoader来加载当前的未知类。何为调用者Class对象？就是在遇到未知类时，虚拟机肯定正在运行一个方法的调用（静态方法或者实例方法），这个方法挂在哪个类上面，那这个类就是调用者Class对象。

之前提到每个Class对象里面都有一个classLoader属性记录了当前的类是由谁来加载的，由于ClassLoader的传递性，所有延迟加载的类都会有初始调用main方法的这个ClassLoader全权负责，它就是AppClassLoader。

#### 双亲委派

JAVA虚拟机规范定义了两种类型的类加载器－启动类加载器(BootstrapClassLoader)和用户自定义类加载器，启动类加载器是JAVA虚拟机实现的一部分，通过继承ClassLoader类，用户可以创建自定义的类加载器来完成特定要求的加载。JAVA虚拟机已经创建了2个自定义类加载器－扩展类加载器(ExtensionClassLoader)和系统类加载器(ApplicationClassLoader)。

其实在调用ClassLoader加载工作的时候，一般都是AppClassLoader负责，但是AppClassLoader在加载之前会调用他的parent，即BootstrapClassLoader 和 ExtensionClassLoader（BootstrapClassLoader是它的parent） 来加载，如果他们都没法加载，最后才是ApplicationClassLoader来加载

这样，使用双亲-孩子委派链的方式，启动类装载器会在最可信的类库－核心Java API－中首先检查每个被装载的类型，然后，才依次到扩展路径、系统类路径中检查被装载的类型文件。用这种方法，类装载器的体系结构就可以防止不可靠的代码用它们自己的版本来替代可以信任的类。

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

#### 名称空间

由不同的类装载器/类加载器加载的类将被放在虚拟机内部的不同命名空间。命名空间由一系列唯一的名称组成，每一个被装载的类有一个名字。JAVA虚拟机为每一个类装载器维护一个名字空间。例如，一旦JAVA虚拟机将一个名为Volcano的类装入一个特定的命名空间，它就不能再装载名为Valcano的其他类到相同的命名空间了。可以把多个Valcano类装入一个JAVA虚拟机中，因为可以通过创建多个类装载器从而在一个JAVA应用程序中创建多个命名空间。

命名空间有助于安全的实现，因为你可以有效地在装入了不同命名空间的类之间设置一个防护罩。在JAVA虚拟机中，在同一个命名空间内的类可以直接进行交互，而不同的命名空间中的类甚至不能觉察彼此的存在，除非显示地提供了允许它们进行交互的机制，如获取Class对象的引用后使用反射来访问。

##### 初始化类装载器/定义类装载器

!（待议/To do）如果要求某个类装载器去装载一个类型，但是却返回了其他类装载器装载的类型，这种装载器被称为是那个类型的初始类装载器 ；而实际装载那个类型的类装载器被称为该类型的定义类装载器 。任何被要求装载类型，并且能够返回Class实例的引用代表这个类型的类装载器，都是这个类型的初始类装载器。比如使用AppClassLoader装载一个类型，但是却返回了BootstrapClassLoader，则BootstrapClassLoader是定义类装载器，而ApplicationClassLoader则是初始化装载器

 虚拟机会为每一个类装载器维护一张列表，列表中是已经被请求过的类型的名字。这些列表包含了每一个类装载器被标记为初始类装载器的类型，它们代表了每一个类装载器的命名空间。虚拟机总是会在调用loadClass()之前检查这个内部列表，如果这个类装载器已经被标记为是这个具有该全限定名的类型的初始类装载器，就会返回表示这个类型的Class实例，这样，虚拟机永远不会自动在同一个用户自定义类装载器上调用同一个名字的类型两次。

##### 命名空间的类型共享

 前面提到过只有同一个命名空间内的类才可以直接进行交互，但是我们经常在由用户自定义类装载器定义的类型中直接使用Java API类，这不是矛盾了吗？这是类型共享 原因－如果某个类装载器把类型装载的任务委派给另外一个类装载器，而后者定义了这个类型，那么被委派的类装载器装载的这个类型，在所有被标记为该类型的初始类装载器的命名空间中共享。

![img](https://img.jbzj.com/file_images/article/201703/201703010851082.gif)

例如上面的例子中，Cindy可以共享Mon、Grandma、启动类装载器的命名空间中的类型，Kenny也可以共享 Mon、Grandma、启动类装载器的 命名空间中的 类型，但是Cindy和Kenny的命名空间不能共享。

##### 运行时包

每个类装载器都有自己的命名空间，其中维护着由它装载的类型。所以一个JAVA程序可以多次装载具有同一个全限定名的多个类型。这样一个类型的全限定名就不足以确定在一个JAVA虚拟机中的唯一性。因此，当多个类装载器都装载了同名的类型时，为了唯一表示该类型，还要在类型名称前加上装载该类型的类装载器来表示－[classloader class]。

 在允许两个类型之间对包内可见的成员进行访问前，虚拟机不但要确定这个两个类型属于同一个包，还必须确认它们属于同一个运行时包－它们必须由同一个类装载器装载的。这样，java.lang.Virus和来自核心的java.lang的类不属于同一个运行时包，java.lang.Virus就不能访问JAVA API的java.lang包中的包内可见的成员。

**更多详细内容，请看[这里](https://www.jb51.net/article/107040.htm)**

#### 自定义类加载器

 JAVA类型要么由启动类装载器装载，要么通过用户自定义的类装载器装载。启动类装载器是虚拟机实现的一部分，它以与实现无关的方式装载类型，JAVA提供了抽象类java.lang.ClassLoader，用户自定义的类装载器是类ClassLoader的子类实例，它以定制的方式装载类。所有用户自定义类装载器都实例化自ClassLoader的子类。

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

