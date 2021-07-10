[toc]

# UML

## 概括

UML--(Unified modeling language)统一建模语言，一种图标式语言，用于软件系统分析和设计的语言工具。它用于帮助软件开发人员进行思考和记录思路的结果，不只是java中使用，只要是面向对象的编程语言，都有UML。在UML图中，可以描述类和类之间的关系，程序执行的流程，对象的状态等。

UML 本身是一套符号的规定，就像数学符号和化学符号一样，这些符号用于描述软件模型中的各个元素和他们之间的关系，比如类、接口、实现、泛化、依赖、组合、聚合等，如下图:

![image-20210707110623872](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5Cimage-20210707110623872.png)

使用UML来建模，常用的工具有Rational Rose，也可以使用一些插件来建模，比如Eclipse安装UML插件（AmaterasUML）

![image-20210707110820903](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5Cimage-20210707110820903.png)

## 分类

1)    用例图(use case)

2)    静态结构图：**类图**、对象图、包图、组件图、部署图

3)    动态行为图：交互图（时序图与协作图）、状态图、活动图

> 平时用的最多的就是类图

## UML类图

类之间的关系：依赖、泛化（继承）、实现、关联、聚合与组合

### 依赖关系

Ø 只要是在类中用到了对方，那么他们之间就存在依赖关系。如果没有对方，连编绎都通过不了。

右下实例类可知，构成依赖关系的有PersonDao、Person、IDCard、Department

```java
public class PersonServiceBean { 
    private PersonDao personDao;//类
    public void save(Person person){}
    public IDCard getIDCard(Integer personid){} 
    public void modify(){
    	Department department = new Department();
	}
}

//定义多个类
public class PersonDao{} 
public class IDCard{} 
public class Person{} 
public class Department{}
```

**对应的类图**

![image-20210707112227490](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5Cimage-20210707112227490.png)

小结：类的成员属性；方法的返回类型；方法接收的参数类型；方法中使用到的变量；这些在类中使用到，或类中方法使用到的，就符合依赖关系

### 泛化关系

Ø 泛化关系实际上就是继承关系，他是依赖关系的特例

```java
public abstract class DaoSupport{ 
    public void save(Object entity){}
	public void delete(Object id){}
}

public class PersonServiceBean extends Daosupport{
}
```

**对应的类图**

![PersonServiceBeanOne](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5CPersonServiceBeanOne.png)

小结：

1)    泛化关系实际上就是继承关系

2)    如果 A 类继承了 B 类，我们就说 A 和 B 存在泛化关系

### 实现关系

实现关系实际上就是 **A** 类实现 **B** 接口，他是依赖关系的特例

```java
public interface PersonService { 
    public void delete(Interger id);
}

public class PersonServiceBean implements PersonService { 
    public void delete(Interger id){}
}

```

**对应的类图**

![PersonServiceBean](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5CPersonServiceBean.png)

### 关联关系

关联关系实际上是类与类之间的联系，它是依赖关系的特例

关联（Association）关系是对象之间的一种引用关系，用于表示一类对象与另一类对象之间的联系，如老师和学生、师傅和徒弟、丈夫和妻子等。关联关系是类与类之间最常用的一种关系，分为一般关联关系、聚合关系和组合关系。我们先介绍一般关联。

#### 聚合和组合之间的区别

其实聚合和组合的关系之间的区别就是组合关系会在类内实例化，这样，该对象就在该类中，而聚合关系则是从其他地方实例化后赋值到该类中，所以他跟该类关系没有那么紧密。

### 聚合关系

聚合关系（Aggregation）表示的是整体和部分的关系，整体与部分可以分开。聚合关系是关联关系的特例，所以他具有关联的导航性与多重性

```java
public class Computer {        
    private Mouse mouse;
    private Keyboard keyboard;
    
    public void setMouse(Mouse mouse) {
        this.mouse = mouse;
    }
    
    public void setKeyboard(Keyboard keyboard) {
        this.keyboard = keyboard;
    }

}
```

**对应的类图**

![Computer](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5CComputer.png)

### 组合关系

组合关系：也是整体与部分的关系，但是整体与部分不可以分开。

再看一个案例：在程序中我们定义实体：Person 与 IDCard、Head, 那么 Head 和 Person 就是 组合，IDCard 和Person 就是聚合。

```java
public class Person{ 
    private IDCard card;
	private Head head = new Head();
}

public class IDCard{}
public class Head{}

```

**对应的类图**

![Person](C:%5CUsers%5Cllj%5CDocuments%5Ctypero%E5%9B%BE%E5%83%8F%5CPerson.png)