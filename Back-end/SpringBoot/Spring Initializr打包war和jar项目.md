使用Spring Initializr打包war和jar项目

相同点：

1. 项目根目录一致
2. 静态资源目录一致
3. 都有一个主程序类，内容一致

不同点：

1. pom.xml文件的打包方式不同，不写默认为jar类型
2. 如果是war打包方式，依赖中需要添加provided类型的tomcat依赖
3. 除了都有一个主程序类，war打包方式在主配置文件夹下还有一个类--ServletInitializer

>ServletInitializer的作用就是用来启动主程序类的，因为war打包方式并不是用主程序类来启动，而是使用serlet来启动，所以借助这个ServletInitializer，来启动主程序类，主程序类就可以便捷地启动程序的其他详细内容。在这里，主程序类成为了servlet程序的一个资源文件。



