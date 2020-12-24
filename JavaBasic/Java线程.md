## 进程和线程

进程是执行着的应用程序，而线程是进程内部的一个执行序列。一个进程可以有多个线程。线程又叫做轻量级进程。

区别：

**a.地址空间和其它资源**：进程间相互独立，同一进程的各线程间共享。某进程内的线程在其它进程不可见。

**b.通信：**进程间通信IPC，线程间可以直接读写进程数据段（如全局变量）来进行通信——需要进程同步和互斥手段的辅助，以保证数据的一致性。

**c.调度和切换**：线程上下文切换比进程上下文切换要快得多。

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

