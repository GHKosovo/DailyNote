# 概述

- 内部类
- 匿名类

# 内部类

- 在一个类中定义另一个类，这样定义的类称为内部类。【包含内部类的类可以称为内部类的外部类】

- 如果想要通过一个类来使用另一个类，可以定义为内部类。【比如苹果手机类，苹果手机类中的黄金版的是特别定制的】

- 内部类的外部类的成员变量在内部类中仍然有效，内部类中的方法也可以调用外部类中的方法。【不论是静态还是非静态的，内部类都可以直接调用外部类中的属性，】

```java
class Outer{
    int a=5;
    static int b=6;
    void show() {
        System.out.println("hello world");
    }
    class Inner{
        void use() {
            System.out.println(a);//5
            System.out.println(b);//6
            show();//hello world
            
        }
    }
    void create() {
        new Inner().use();
    }

}

public class Demo {

    public static void main(String[] args) {
        new Outer().create();
        Outer.Inner oi=new Outer().new Inner();
        oi.use();

    }

}
```

- 内部类的类体中不可以声明类变量和类方法

![1053079-20180325144825344-388918570](C:%5CUsers%5Cllj%5CPictures%5C1053079-20180325144825344-388918570.png)

- 内部类可以由外部类使用外部类中在函数中创建内部类对象来使用，如果内部类的权限是非私有，非静态的，就可以在外部其他程序中被访问到，就可以通过创建外部类对象完成，如果内部类是静态的，非私有的，静态成员可以直接类名调用，非静态成员通过创建外部类对象使用。

```java
//内部类可以由外部类使用外部类中在函数中创建内部类对象来使用
void create(){
    new Inner().use();
}

//如果内部类的权限是非私有，非静态的，就可以在外部其他程序中被访问到，就可以通过创建外部类对象完成
Outer.Inner oi = new Outer().new Inner();
oi.use();

//如果内部类是静态的，非私有的，静态成员可以直接类名调用，非静态成员通过创建外部类对象使用。
class Fu{
    static class Zi{
        int num = 4;
        static int num2 = 5;
        void show(){
            System.out.println("run");
        }
    }
}

class Demo{
    public static void main(String[] args){
        //System.out.pirntln(Fu.Zi.num);
        //1.无法从静态上下文中引用非静态类变量num,num加上static才可运行。
        
        //可行
        System.out.println(Fu.Zi.num2);
        
        //Fu.Zi.show();
        //2.无法从静态上下文中引用非静态方法show()
        
        Fu Zi i = new Fu.Zi();
        i.show();
        //可行
    }
}
```

## :heavy_plus_sign:补充

- 内部类的字节码文件会不一样。会变成外部类名$内部类名

![1053079-20180325144827263-2000887696](C:%5CUsers%5Cllj%5CPictures%5C1053079-20180325144827263-2000887696.png)

- 将内部类定义在了局部位置上
  
  ```java
  class Outer{
      private int num = 4;
      Object obj;
      void use(){
          int num2 =6;
          class Inner{
              //static int num3 = 8;内部类中不能定义static
              void show(){
                  System.out.println("Fu "+num2+".."+num);
                  //jdk1.8以上不会警告num2要加final
              }
          }
          Inner in = new Inner();
          in.show();//要放在局部里面
      }
  }
  
  class Demo{
      public static void main(String[] args){
          new Outer().use();//Fu 6..4
      }
  }
  ```
  
  - 可以访问外部类的所有成员
  - 局部内部类不能访问所在局部的局部变量（本来他们生命周期不同，本来内部类对象的空间没有消失，对象生命长）若需访问，加final修饰变量即可，加final变成常量。（jdk1.8自动修饰了final）
  
- 内部类如果定义成静态的，那么生命周期跟普通的static没什么区别。

# 匿名类

- 匿名类，就是没有名称的类，其名称由Java编译器给出，一般是形如：外部类名称+$+匿名类顺序，没有名称也就是其他地方就不能引用，不能实例化，只用一次，当然也就不能有构造器。

- 匿名类就是利用父类的构造函数和自身类体构造成一个类。

   **格式：new 父类（）{子类内容}**
  - 上面格式中的“父类”是子类需要继承或者实现的外部类或者接口

- 匿名类可以继承父类的方法，也可以重写父类的方法。

- 匿名类可以访问外部类的成员变量和方法，匿名类的类体不可以声明static成员变量和static方法。

- 匿名类由于是一个new的结果，所以其实可以赋值给一个父类对象。因此可以分为两种匿名类，成员匿名类和局部匿名类（作为函数参数）

  ```java
  Out ot = new Outer(){
      void show(){
          System.out.println("run in Inner");
      }
  };
  ```



实现接口方法的例子

```java
interface Culic{
    double getCubic(int n);
}
interface Sqrt{
    public abstract double getSqrt(int x);
}
class A{
    void f(Cubic cubic){
        dobule result = cubic.getCubic(3);
        System.out.println(result);
    }
}
class Demo{
    public static void main(String[] args){
        A a = new A();
        a.f(new Cubic(){
            public double getCubic(int n){
                return n*n*n;
            }
        });
        Sqrt ss = new Sqrt(){
            public double getSqrt(int x){
                return Math.sqrt(x);
            }
        };
        double m = ss.getSqrt(5);
        System.out.println(m);
        //27.0			2.23606797749979
    }
}
```

```java
class Outer{
    void show() {
        System.out.println("run in Outer");
    }
}

public class Demo {
    public static void main(String args[]) {
        Outer ot=new Outer(){
        void show() {
            System.out.println("run in Inner");
        }
    };
    ot.show();//run in Inner
    }
}
```

#抄自[随风行云的博客](https://www.cnblogs.com/progor/p/8644634.html)

