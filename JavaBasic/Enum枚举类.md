# Java中的枚举类

## 一、概述

1. 在某些情况下，一个类的对象时有限且固定的，如季节类，它只有春夏秋冬4个对象这种实例有限且固定的类，在 Java 中被称为枚举类；
2. 在 Java 中使用 enum 关键字来定义枚举类，其地位与 class、interface 相同；
3. 枚举类是一种特殊的类，它和普通的类一样，有自己的成员变量、成员方法、构造器 (只能使用 private 访问修饰符，所以无法从外部调用构造器，构造器只在构造枚举值时被调用)；
4. 一个 Java 源文件中最多只能有一个 public 类型的枚举类，且该 Java 源文件的名字也必须和该枚举类的类名相同，这点和类是相同的；
5. 使用 enum 定义的枚举类默认继承了 java.lang.Enum 类，并实现了 java.lang.Seriablizable 和 java.lang.Comparable 两个接口;
6. 所有的枚举值都是 public static final 的，且非抽象的枚举类不能再派生子类；
7. 枚举类的所有实例(枚举值)必须在枚举类的第一行显式地列出，否则这个枚举类将永远不能产生实例。列出这些实例(枚举值)时，系统会自动添加 public static final 修饰，无需程序员显式添加。

## 二、枚举类的使用

### 定义枚举类

```java
// 定义一个星期的枚举类
public enum WeekEnum {
    // 在第一行显式地列出7个枚举实例(枚举值)，系统会自动添加 public static final 修饰
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY;
} 
```

### 枚举类的成员变量、成员方法、构造器

```java
package enumtest;

public enum WeekEnum {
    
    // 因为已经定义了带参数的构造器，所以在列出枚举值时必须传入对应的参数
    SUNDAY("星期日"), MONDAY("星期一"), TUESDAY("星期二"), WEDNESDAY("星期三"), 
    THURSDAY("星期四"), FRIDAY("星期五"), SATURDAY("星期六");

    // 定义一个 private 修饰的实例变量
    private String date;

    // 定义一个带参数的构造器，枚举类的构造器只能使用 private 修饰
    private WeekEnum(String date) {
        this.date = date;
    }

    // 定义 get set 方法
    public String getDate() {
        return date;
    }

    public void setDate(String date) {
        this.date = date;
    }
    
}
```

### 枚举类中的常用方法

1. **String name()：** 返回此枚举实例的名称，即枚举值 ；
2. **static values()：** 返回一个包含全部枚举值的数组，可以用来遍历所有枚举值；
3. **String toString()：** 返回枚举值的名称，与 name 方法类似，更常用；

#摘抄自[MonkeyChenn的博客](https://www.jianshu.com/p/46dbd930f6a2)