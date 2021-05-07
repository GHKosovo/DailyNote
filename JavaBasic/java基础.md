### String字符串判空

对于`String`类型判断它是否为空:point_up:，要使用**`isEmpty()`**这个方法或**`string==""`**，不能用**`string == Null`**,因为`String`是一个对象，不可以判`Null`

<!--more-->

### 枚举类:notebook:

[参考文档](https://www.jianshu.com/p/46dbd930f6a2)

### 静态类和静态方法

静态类一定是一个内部类,静态类的属性和方法也都需要是静态的。

静态方法内部的属性都需要时静态的。静态方法可以直接跨类调用。直接引用该类的方法就可以了。

[参考文档](https://www.jianshu.com/p/80b404da976b)

### super()

经常在实现无参构造函数的时候遇见这家伙:boy:，这个`super`其实代表的是父类对象，通过`super`可以获取到父类:man:的属性、方法和构造函数；

super.属性名                调用父类属性

super.fun_name()        调用符类方法

super()/super(参数)     调用父类构造函数

### PrintWriter中write()和print()的区别

其实都是输出内容:speech_balloon:，但是`print()`还可以将各种类型的数据转化成字符串的形式输出。(最后再转换成字节输出)

### 新建对象:elephant:

在使用`xstream`的时候，发现新建对象时后面还可以有代码块，在代码块里面可以调用这个对象的方法获取新建对象所需要的东西（以前真没遇到过这种东西）	

```java
//自定义xml对象格式
	private static XStream xStream = new XStream(new XppDriver() {
		public HierarchicalStreamWriter createWriter(Writer out) {
			
			return new PrettyPrintWriter(out) {
				boolean cdata = true;
				
				public void startNode(String name, Class clazz) {
					super.startNode(name, clazz);
				}
				......
```