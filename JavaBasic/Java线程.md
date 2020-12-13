## 定义线程
1. 实现runnable接口（需要写构造函数）
2. 继承Thread类（重写run方法）

### 实例化线程

- 使用**实现runnable接口**的方法，则用Thread的构造方法

Thread(Runnable target)
Thread(Runnable target, String name)
Thread(ThreadGroup group, Runnable target)
Thread(ThreadGroup group, Runnable target, String name)
Thread(ThreadGroup group, Runnable target, String name, long stackSize)

- 使用**继承Thread类**的方法，直接new一个Thread对象

### 启动线程

在线程的Thread对象上调用start()方法，而不是run()或者别的方法。

run()其实只是执行线程中的运行代码而已！

