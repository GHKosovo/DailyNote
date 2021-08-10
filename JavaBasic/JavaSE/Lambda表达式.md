## 概述
(**译者注**:虽然看着很先进，其实Lambda表达式的本质只是一个"[**语法糖**](http://zh.wikipedia.org/wiki/语法糖)",由编译器推断并帮你转换包装为常规的代码,因此你可以使用更少的代码来实现同样的功能。建议不要乱用,因为这就和某些很高级的黑客写的代码一样,简洁,难懂,难以调试,维护人员想骂娘.)
Lambda表达式是Java SE 8中一个重要的新特性。lambda表达式允许你通过表达式来代替功能接口。 lambda表达式就和方法一样,它提供了一个正常的参数列表和一个使用这些参数的主体(body,可以是一个表达式或一个代码块)。

Lambda表达式还增强了集合库。 Java SE 8添加了2个对集合数据进行批量操作的包: java.util.function 包以及java.util.stream 包。 流(stream)就如同迭代器(iterator),但附加了许多额外的功能。 总的来说,lambda表达式和 stream 是自Java语言添加泛型(Generics)和注解(annotation)以来最大的变化。 在本文中,我们将从简单到复杂的示例中见认识lambda表达式和stream的强悍。

## 什么是lambda表达式

可以将Lambda表达式理解为一个匿名函数； Lambda表达式允许将一个函数作为另外一个函数的参数； 我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码作为实参）,也可以理解为函数式编程，将一个函数作为参数进行传递。



## 为什么要引入Lambda表达式

当java程序员看到其他语言的程序员（如JS，Python）在使用闭包或者Lambda表达式的时候，于是开始吐槽世界上使用最广的语言居然不支持函数式编程。千呼万唤，Java8推出了Lambda表达式。

Lambda表达式能够让程序员的编程更加高效

让我们先来看一段代码：

```java
package com.isea.java;
public class TestLambda {
       public static void main(String[] args) {
       Thread thread = new Thread(new MyRunnable());
       thread.start();
       thread.close();
    }
}
class MyRunnable implements Runnable{
	    @Override
        public void run() {
            System.out.println("Hello");
        }
}
```

为了使这段代码变得更加简洁，可以使用匿名内部类重构一下（注意代码中的注释）

```java
package com.isea.java;
public class TestLambda {
    public static void main(String[] args) {
        new Thread(new Runnable() {
        //这里的new Runnable()，这里new 了接口，在这个new的接口里面，我们写了这个接口的实现类。
        //这里可以看出，我们把一个重写的run()方法传入了一个构造函数中。
            @Override
            public void run() {
                System.out.println("Hello");
            }
        }).start();
    }
}
```

上面的代码可以换成这样的，我们将new 的接口，赋值给线程的引用：

```java
package com.isea.java;
 
public class TestLambda {
    public static void main(String[] args) {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello");
            }
        });
        thread.start();
    }
}
```

而上面的这段代码，不是最简单的，还可以进一步简化



```java
package com.isea.java;
public class TestLambda {
    public static void main(String[] args) {
        new Thread(() -> System.out.println("Hello")).start();
    }
}
```

> 这里new Thread(() -> System.out.println("Hello")); 同样实现了将一段代码传入了构造方法中

怎么样？是不是很酷？我想上面的三段代码已经成功的想你展示了Lambda表达式能够让程序员的编程更叫高效这话是真的！假如之前没有接触过函数式编程的话，即便已经体会到了Lambda表达式能够减少代码量，省掉不少手力，也为这逻辑，感到很费脑力。孰能生巧，还记得当年打9*9的乘法表的时候，也觉得很难不是么？



### Lambda表达式的语法
([Lambda参数列表，即形参列表]) -> {Lambda体，即方法体}

拷贝小括号，写死右箭头，落地大括号，大括号中写上业务逻辑

特点：使用 "->"将参数和实现逻辑分离；( ) 中的部分是需要传入Lambda体中的参数；{ } 中部分，接收来自 ( ) 中的参数，完成一定的功能。

### Lambda表达式的分类

- 无参无返回值

```java
package com.isea.java;
public class TestLambda {
    public static void main(String[] args) {
        new Thread(() -> System.out.println("Hello"));
    }
}
```

> ()中无参数，也不能省略；{}中只有一句话，建议省略。



- 有参无返回值

```java
package com.isea.java;
import java.util.ArrayList;
public class TestLambda {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("AAAAA");
        list.add("BBBBB");
        list.add("CCCCC");
        list.add("DDDDD");
		//形参的类型是确定的，可省略；只有一个形参，()可以省略；
        list.forEach(t -> System.out.print(t + "\t"));
		//或者更简洁的方法引用：list.forEach(System.out::println);
        //打印结果：AAAAA	BBBBB	CCCCC	DDDDD
    }
}
public void forEach(Consumer<? super E> action)
```

forEach() 功能等同于增强型for循环 这个方法来自于Iterable接口，Collection接口继承了这个接口，List又继承了Collection接口，而ArrayList是List的实现类；forEach函数，指明该函数需要传入一个函数，而且是有参数没有返回值的函数，而Consumer接口中正好有且仅有一个这样的有参无返回值的抽象方法。接下来，我们会了解到这是使用Lambda的必要条件。

```java
void accept(T t);	// from source code
```



- 无参有返回值

```java
package com.isea.java;
import java.util.Random;
import java.util.stream.Stream;
public class TestLambda {
    public static void main(String[] args) {
        Random random = new Random();
        Stream<Integer> stream = Stream.generate(() ->random.nextInt(100));
        stream.forEach(t -> System.out.println(t));
    }//只有一个return，可以省略return；该方法将会不断的打印100以内的正整数。
}//Stream.generate()方法创建无限流，该方法要求传入一个无参有返回值的方法。
public static<T> Stream<T> generate(Supplier<T> s) //来自源码
```



- 有参有返回值

需求：按照学生的姓名对学生进行排序（使用Collator 文本校对器排序）

```java
package com.isea.java;
import java.text.Collator;
import java.util.TreeSet;
 
public class TestLambda {
    public static void main(String[] args) {
        Collator collator = Collator.getInstance();
        TreeSet<Student> set = new TreeSet<>((s1,s2) -> collator.compare(s1.getName(),s2.getName()));
        set.add(new Student(10,"张飞"));
        set.add(new Student(3,"周瑜"));
        set.add(new Student(1,"宋江"));
        set.forEach(student -> System.out.println(student));
    }
}//这里的Collator是一个抽象类，但是提供了获取该类实例的方法getInstance()
 
class Student{
    private int id;
    private String name;
    
    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
 
    public int getId() {
        return id;
    }
 
    public void setId(int id) {
        Home | This.ID = id;
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

Lambda表达式的分类，参数和返回值交叉组合一共四种。





### Java lambda表达式的简单例子：

```java
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```



## 函数式接口

即SAM（Single Abstract Method ）接口，有且只有一个抽象方法的接口（可以有**默认方法**或者是**静态方法**和**从Object继承来的方法**，但是**抽象方法有且只能有一个**）。 JDK1.8之后，添加@FunctionalInterface表示这个接口是一个函数式接口，因为有了@functionalInterface标记，也称这样的接口为Mark（标记）类型的接口。举例子：

```java
@FunctionalInterface
public interface Runnable {
	  public abstract void run();
}//来自源码
```

只有函数式接口的变量或者是函数式接口，才能够赋值为Lambda表达式。这个接口中，可以有默认方法，或者是静态方法。函数式接口中还可以有Object中覆盖的方法，也就是equals方法，hashCode方法。 JDK1.8之后，如果是函数式接口，可以添加 @FunctionalInterface表示这是一个函数式接口，举例：



```text
@FunctionalInterface
java.lang.Runnable{
    void run();
}
	   
@FunctionalInterface
java.lang.Comparator<T>{
	int compare(T o1, T o2);
}
 
@FunctionalInterface
public interface Function<T, R> {
	R apply(T t);
}
```

针对上面的例子，比方说这个Runnable接口是支持Lambda表达式，那么如果有一个方法（比如Thread类的构造函数）需要传入一个Runnable接口的实现类的话，那么就可以直接把Lambda表达式写进去。

换个角度说TreeSet，它有一个构造函数中是要求传入一个接口类型，如果这个接口类型恰好是函数式接口，那么直接传进去一个Lambda表达式即可。

### 函数式接口有什么作用？

函数式接口能够接受匿名内部类来实例化对象，换句话说，我们可以使用匿名内部类来实例化函数式接口的对象，而Lambda表达式能够代替内部类实现代码的进一步简化，因此，Lambda表达式和函数式接口紧密的联系到了一起，接下来的这句话非常的重要：

**每一个Lambda表达式能隐式的给函数式接口赋值**

编译器会认为Thread()中传入的是一个Runnable的对象，而我们利用IDEA的智能感知，鼠标指向“->”或“（）”的时候，会发现这是一个Runnable类型，实际上编译器会自动将Lambda表达式赋值给函数式接口，在本例中就是Runnable接口。本例中Lambda表达式将打印方法传递给了Runnable接口中的run（）方法，从而形成真正的方法体。

而且，

参数与返回值是一一对应的，即如果函数式接口中的抽象方法是有返回值，有参数的，那么要求Lambda表达式也是有返回值，有参数的（余下类推）



#### 自定义一个函数式接口

```java
package com.isea.java;
@FunctionalInterface
public interface IMyInterface {
    void study();
}
 
package com.isea.java;
public class TestIMyInterface {
    public static void main(String[] args) {
        IMyInterface iMyInterface = () -> System.out.println("I like study");
        iMyInterface.study();
    }
}//这里的Lambda表达式将方法体赋值给函数式接口
```

### 四大函数式接口	

有时候后，如果我们调用某一个方法，发现这个方法中需要传入的参数要求是一个函数式的接口，那么我们可以直接传入Lambda表达式。这些接口位于java.util.function包下，需要注意一下，java.util包和java.util.function包这两个包没有什么关系，切不可以为function包是java.util包下面的包。

1. 消费型接口：Consumer< T> void accept(T t)有参数，无返回值的抽象方法；
2. 供给型接口：Supplier < T> T get() 无参有返回值的抽象方法；
3. 断定型接口： Predicate< T> boolean test(T t):有参，但是返回值类型是固定的boolean
4. 函数型接口： Function< T，R> R apply(T t)有参有返回值的抽象方法；

除了这四个之外，在java.util.function包下还有很多函数式接口可供使用。

例子：如果薪资小于10000，涨工资到10000

```java
package com.isea.java;
import java.util.HashMap;
public class TestLambda {
    public static void main(String[] args) {
        HashMap<String,Double> map = new HashMap<>();
        map.put("周瑜",9000.0);
        map.put("宋江",12000.0);
        map.put("张飞",8000.0);
 
        map.forEach((k,v) -> {
            if (v < 10000.0)
                map.put(k,10000.0);
        });//BiConsumer<T,U>，void apply(T t, U u)
        map.forEach((k,v) -> System.out.print(k + ":" + v + "\t\t"));
    }
}//结果打印：张飞:10000.0		周瑜:10000.0		宋江:12000.0	
```



## 方法引用--待看懂210810

当Lambda表达式满足某种条件的时候，使用方法引用，可以再次简化代码

构造引用：当Lambda表达式是通过new一个对象来完成的，那么可以使用构造引用。

```java
package com.isea.java;
import java.util.function.Supplier;
public class TestLambda {
    public static void main(String[] args) {
//        Supplier<Student> s = () -> new Student();
 
        Supplier<Student> s = Student::new;
    }//实际过程：将new Student()赋值给了Supplier这个函数式接口中的那个抽象方法
}
```



### 类名::实例方法

Lambda表达式的的Lambda体也是通过一个对象的方法完成，但是调用方法的对象是Lambda表达式的参数列表中的一个，剩下的参数正好是给这个方法的实参。

```java
package com.isea.java;
import java.util.TreeSet;
public class TestLambda {
    public static void main(String[] args) {
//        TreeSet<String> set = new TreeSet<>((s1,s2) -> s1.compareTo(s2));
```

> 这里如果使用第一句话，编译器会有提示：Can be replaced with Comparator.naturalOrder，这句话告诉我们



String已经重写了compareTo()方法，在这里写是多此一举，这里为什么这么写，是因为为了体现下面

这句编译器的提示：Lambda can be replaced with method reference。

好了，下面的这句就是改写成方法引用之后：

```java
//类名::实例方法
        TreeSet<String> set = new TreeSet<>(String::compareTo);
        set.add("Hello");
        set.add("isea_you");
       // set.forEach(t -> System.out.println(t));  //Hello \n isea_you
        set.forEach(System.out::println);
        //（1）对象::实例方法,Lambda表达式的(形参列表)与实例方法的(实参列表)类型，个数是对应
    }
}
```

### 对象::实例方法

上面的代码的最后一句已经演示。



### 类名::静态方法

```java

package com.isea.java;
import java.util.stream.Stream;
public class TestLambda {
    public static void main(String[] args) {
		//Stream<Double> stream = Stream.generate(() -> Math.random());
		//类名::静态方法, Lambda表达式的(形参列表)与实例方法的(实参列表)类型，个数是对应
        Stream<Double> stream = Stream.generate(Math::random);
        stream.forEach(System.out::println);
    }
}
```



## 案例与实践

现在,我们已经知道什么是lambda表达式,让我们先从一些基本的例子开始。 在本节中,我们将看到lambda表达式如何影响我们编码的方式。 假设有一个玩家List ,程序员可以使用 for 语句 ("for 循环")来遍历,在Java SE 8中可以转换为另一种形式:

```java
String[] atp = {"Rafael Nadal", "Novak Djokovic",  
       "Stanislas Wawrinka",  
       "David Ferrer","Roger Federer",  
       "Andy Murray","Tomas Berdych",  
       "Juan Martin Del Potro"};  
List<String> players =  Arrays.asList(atp);  
  
// 以前的循环方式  
for (String player : players) {  
     System.out.print(player + "; ");  
}  
  
// 使用 lambda 表达式以及函数操作(functional operation)  
players.forEach((player) -> System.out.print(player + "; "));  
   
// 在 Java 8 中使用双冒号操作符(double colon operator)  
players.forEach(System.out::println);
```



正如您看到的,lambda表达式可以将我们的代码缩减到一行。 另一个例子是在图形用户界面程序中,匿名类可以使用lambda表达式来代替。 同样,在实现Runnable接口时也可以这样使用:

```java
// 使用匿名内部类  
btn.setOnAction(new EventHandler<ActionEvent>() {  
          @Override  
          public void handle(ActionEvent event) {  
              System.out.println("Hello World!");   
          }  
    });  
   
// 或者使用 lambda expression  
btn.setOnAction(event -> System.out.println("Hello World!"));
```



下面是使用lambda 来实现 Runnable接口 的示例:

```java
// 1.1使用匿名内部类  
new Thread(new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("Hello world !");  
    }  
}).start();  
  
// 1.2使用 lambda expression  
new Thread(() -> System.out.println("Hello world !")).start();  
  
// 2.1使用匿名内部类  
Runnable race1 = new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("Hello world !");  
    }  
};  
  
// 2.2使用 lambda expression  
Runnable race2 = () -> System.out.println("Hello world !");  
   
// 直接调用 run 方法(没开新线程哦!)  
race1.run();  
race2.run();
```



Runnable 的 lambda表达式,使用块格式,将五行代码转换成单行语句。 接下来,在下一节中我们将使用lambda对集合进行排序。
**使用Lambdas排序集合**
在Java中,Comparator 类被用来排序集合。 在下面的例子中,我们将根据球员的 name, surname, name 长度 以及最后一个字母。 和前面的示例一样,先使用匿名内部类来排序,然后再使用lambda表达式精简我们的代码。
在第一个例子中,我们将根据name来排序list。 使用旧的方式,代码如下所示:

```java
String[] players = {"Rafael Nadal", "Novak Djokovic",   
    "Stanislas Wawrinka", "David Ferrer",  
    "Roger Federer", "Andy Murray",  
    "Tomas Berdych", "Juan Martin Del Potro",  
    "Richard Gasquet", "John Isner"};  
   
// 1.1 使用匿名内部类根据 name 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.compareTo(s2));  
    }  
});
```



使用lambda,可以通过下面的代码实现同样的功能:

```java
// 1.2 使用 lambda expression 排序 players  
Comparator<String> sortByName = (String s1, String s2) -> (s1.compareTo(s2));  
Arrays.sort(players, sortByName);  
  
// 1.3 也可以采用如下形式:  
Arrays.sort(players, (String s1, String s2) -> (s1.compareTo(s2))); 
```



其他的排序如下所示。 和上面的示例一样,代码分别通过匿名内部类和一些lambda表达式来实现Comparator :

```java
// 1.1 使用匿名内部类根据 surname 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.substring(s1.indexOf(" ")).compareTo(s2.substring(s2.indexOf(" "))));  
    }  
});  
  
// 1.2 使用 lambda expression 排序,根据 surname  
Comparator<String> sortBySurname = (String s1, String s2) ->   
    ( s1.substring(s1.indexOf(" ")).compareTo( s2.substring(s2.indexOf(" ")) ) );  
Arrays.sort(players, sortBySurname);  
  
// 1.3 或者这样,怀疑原作者是不是想错了,括号好多...  
Arrays.sort(players, (String s1, String s2) ->   
      ( s1.substring(s1.indexOf(" ")).compareTo( s2.substring(s2.indexOf(" ")) ) )   
    );  
  
// 2.1 使用匿名内部类根据 name lenght 排序 players  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.length() - s2.length());  
    }  
});  
  
// 2.2 使用 lambda expression 排序,根据 name lenght  
Comparator<String> sortByNameLenght = (String s1, String s2) -> (s1.length() - s2.length());  
Arrays.sort(players, sortByNameLenght);  
  
// 2.3 or this  
Arrays.sort(players, (String s1, String s2) -> (s1.length() - s2.length()));  
  
// 3.1 使用匿名内部类排序 players, 根据最后一个字母  
Arrays.sort(players, new Comparator<String>() {  
    @Override  
    public int compare(String s1, String s2) {  
        return (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1));  
    }  
});  
  
// 3.2 使用 lambda expression 排序,根据最后一个字母  
Comparator<String> sortByLastLetter =   
    (String s1, String s2) ->   
        (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1));  
Arrays.sort(players, sortByLastLetter);  
  
// 3.3 or this  
Arrays.sort(players, (String s1, String s2) -> (s1.charAt(s1.length() - 1) - s2.charAt(s2.length() - 1)));
```

> 关于stream不做说明，详细内容请看下面博客了解详情

#抄自[Franson的博客](https://www.cnblogs.com/franson-2016/p/5593080.html)

