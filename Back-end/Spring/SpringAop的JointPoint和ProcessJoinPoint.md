## 概念

### Joint Point
JointPoint是程序运行过程中可识别的点，这个点可以用来作为AOP切入点。JointPoint对象则包含了和切入相关的很多信息。比如切入点的对象，方法，属性等。我们可以通过反射的方式获取这些点的状态和信息，用于追踪tracing和记录logging应用信息。

### Pointcut
pointcut 是一种程序结构和规则，它用于选取join point并收集这些point的上下文信息。
pointcut通常包含了一系列的Joint Point，我们可以通过pointcut来同时操作jointpoint。单从概念上，可以把Pointcut当做jointpoint的集合。

## JointPoint和ProceedingJoinPoint区别

### Joint Point

通过JpointPoint对象可以获取到下面信息

```java
// 返回目标对象，即被代理的对象
Object getTarget();

// 返回切入点的参数
Object[] getArgs();

// 返回切入点的Signature
Signature getSignature();

// 返回切入的类型，比如method-call，field-get等等，感觉不重要 
 String getKind();

```

### ProceedingJoinPoint

Proceedingjoinpoint 继承了 JoinPoint。它是在JoinPoint的基础上暴露出 proceed 这个方法。proceed很重要，这个是aop代理链执行的方法。

> 环绕通知=前置+目标方法执行+后置通知，proceed方法就是用于启动目标方法执行的

```markdown
暴露出这个方法，就能支持 aop:around 这种切面（而其他的几种切面只需要用到JoinPoint，，这也是环绕通知和前置、后置通知方法的一个最大区别。这跟切面类型有关）， 能决定是否走代理链还是走自己拦截的其他逻辑。建议看一下 JdkDynamicAopProxy的invoke方法，了解一下代理链的执行原理。
```

## JointPoint使用详解
这里详细介绍JointPoint的方法，这部分很重要是coding核心参考部分。开始之前我们思考一下，我们到底需要获取切入点的那些信息。我的思考如下

1. 切入点的方法名字及其参数
2. 切入点方法标注的注解对象（通过该对象可以获取注解信息）
3. 切入点目标对象（可以通过反射获取对象的类名，属性和方法名）

> 注：有一点非常重要，Spring的AOP只能支持到方法级别的切入。换句话说，切入点只能是某个方法。

针对以上的需求JDK提供了如下API

#### 1 获取切入点所在目标对象

```java
Object targetObj =joinPoint.getTarget();

//可以发挥反射的功能获取关于类的任何信息，例如获取类名如下
String className = joinPoint.getTarget().getClass().getName();
```

#### 2.获取切入店方法的名字

```java
//getSignature()是获取到这样的信息:修饰符+包名+组件名(类名)+方法名

//获取方法名
String methodName = joinPoint.getSignature().getName();
```

#### 3.获取方法的参数

```java
Object[] args = joinPoint.getArgs();
```



