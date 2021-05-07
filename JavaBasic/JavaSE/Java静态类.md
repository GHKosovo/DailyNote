[TOC]

## 一、静态类是什么？

其实它是内部类的分支，也就是说，它是一个内部类。



## 二、静态内部类和非静态内部类的区别

### 1.类的声明方式

> 静态类：public static class StaticClass{}
>
> 非静态类：public class ClassName{}



### 2.访问类内部成员的权限

>静态类：只能访问类内部的静态成员
>
>非静态类：可访问类内的所有成员



### 3.声明类内部成员的权限

>  静态类：可以声明静态成员和非静态成员
>
> 非静态类：只能声明非静态成员 



### 4.类的初始化方式

> 假设，类结构为：Outer类内包含Inner类，InnerStatic类为Outer类的内部静态类，Inner为非静态
> #### 静态类的初始化：
>  `Outer.InnerStatic innerStatic = new  Outer.InnerStatic();`
>
>
>  #### 非静态类初始化：
>
>  `Outer.Inner inner = new Outer().new Inner();`
>
>  or
>  `Outer outer = new Outer();`
>
>  `Outer.Inner inner = outer.new Inner();`



## 三、何时使用静态类？

### 情况一

当A类需要使用B类，并且B某类仅为A类服务，那么就没有必要单独写一个B类，因为B类在其他类中不会使用，所以只需将B类作为A的内部类。那么为什么一定要用静态类呢？通过上面`类初始化方式`可以看出，内部静态类的初始化无需实例化外部类，与对象无关；所以说这种情况推荐将B类声明为A类的内部静态类

```JAVA
public class Earth {
    private Water water;

    public Earth(Water w){
        water = w;
    }

    public int getNumber(){
        return water.getNum();
    }

    public String getContent(){
        return Water.getContent();
    }

    public static class Water{
        int num;

        public Water(int num){
            this.num = num;
        }
        public int getNum(){
            return num;
        }
        public static  String getContent(){
            return "H2O";
        }
    }
}
```

测试类

```java
public class Main {
    public static void main(String[] args) {
        Earth earth = new Earth(new Earth.Water(78));
        String content = earth.getContent();
        int number = earth.getNumber();
    }
}
```



### 情况二

当某哦个类需要接受多个参数进行初始化时，推荐使用静态类构建

#### 没有使用静态类的例子

```java
public class Car {
    private String name;
    private String model;
    private int height;
    private int width;

    public Car(String name){
        this.name=name;
    }
    ......
    //这里省略了 若干Car类的构造函数
    public Car(String name,String model,int height,int width){
        this.name=name;
        this.model=model;
        this.height=height;
        this.width=width;
    }
    ......
}
```

下面调用可以看出，未使用静态类的初始化构造函数 初始化变量很不灵活。

```java
public class test {
    Car car1 = new Car("捷达王","大众捷达",1650，1950);
    Car car2 = new Car("皇冠");
     car2.setModel("丰田皇冠3.0");
     car2.setHeight("1920");
}
```

这个例子的变量只有4个，假如有40个变量呢，这40个变量又有选择性的初始化呢？那么必须使用静态类进行构建！下面是使用静态类的实现：

```java

public class Car {
    private String name;
    private String model;
    private int height;
    private int width;

    private Car(Builder build){
        this.name=build.name;
        this.model= build.model;
        this.height=build.height;
        this.width=build.width;
    }
    public static class Builder {
        private String name;
        private String model;
        private int height;
        private int width;
        public Builder(){

        }
        public Builder withName(String name){
            this.name=name;
            return this;
        }
        public Builder withModel(String model){
            this.model=model;
            return this;
        }
        public Builder withHeight(int height){
            this.height=height;
            return this;
        }
        public Builder withWidth(int width){
            this.width=width;
            return this;
        }
        public Car build(){
            return new Car(this);
        }
    }
}
```

测试代码

```java
public class test {
    Car jettaCar = 
        new Car.Builder().withName("捷达王")
                         .withModel("大众捷达")
                         .withHeight(1650)
                         .withWidth(1950)
                         .build();
    Car toyotaCar = 
        new Car.Builder().withName("皇冠")
                         .withModel("丰田皇冠3.0")
                         .withHeight(1920)
                         .build();
}
```



## 总结

静态类一定是内部类。

静态类只能访问外部类的静态成员。

内部类中只有静态类才可以声明静态成员。