# 概述
(**译者注**:虽然看着很先进，其实Lambda表达式的本质只是一个"[**语法糖**](http://zh.wikipedia.org/wiki/语法糖)",由编译器推断并帮你转换包装为常规的代码,因此你可以使用更少的代码来实现同样的功能。本人建议不要乱用,因为这就和某些很高级的黑客写的代码一样,简洁,难懂,难以调试,维护人员想骂娘.)
Lambda表达式是Java SE 8中一个重要的新特性。lambda表达式允许你通过表达式来代替功能接口。 lambda表达式就和方法一样,它提供了一个正常的参数列表和一个使用这些参数的主体(body,可以是一个表达式或一个代码块)。

Lambda表达式还增强了集合库。 Java SE 8添加了2个对集合数据进行批量操作的包: java.util.function 包以及java.util.stream 包。 流(stream)就如同迭代器(iterator),但附加了许多额外的功能。 总的来说,lambda表达式和 stream 是自Java语言添加泛型(Generics)和注解(annotation)以来最大的变化。 在本文中,我们将从简单到复杂的示例中见认识lambda表达式和stream的强悍。

### Lambda表达式的语法
**基本语法:**
**(parameters) -> expression**
或
**(parameters) ->{ statements; }**

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

### 基本的Lambda例子
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



下面是使用lambdas 来实现 Runnable接口 的示例:

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



Runnable 的 lambda表达式,使用块格式,将五行代码转换成单行语句。 接下来,在下一节中我们将使用lambdas对集合进行排序。
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



使用lambdas,可以通过下面的代码实现同样的功能:

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

