#### 如何获取Classpath下的文件？

使用Spring核心库的`ClassPathResource`类可以直接获取到Classpath路径下的文件，也即是resource文件夹下文件。

> 如果是单元测试，那么它会先去测试的资源目录`project/src/test/resources`去寻找,没有找到就会去项目资源目录`/project/src/main/resources`去找

顺便介绍下同样在Spring核心库里的一个类FileSystemResource，这个类在找文件的时候是按照给定的路径去找的，默认的当前目录就是项目根目录。

```java
new FileSystemResource("./");	//项目的目录
new FileSystemResource("/");	//系统根目录
new FileSystemResource("src/main/resources/generatorConfig.xml");	//获取resources文件夹下的文件generatorConfig.xml
```



