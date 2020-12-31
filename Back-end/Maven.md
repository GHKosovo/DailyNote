## Maven

### 编译

```shell
mvn compile
```

编译会下载所有的插件和相关的依赖，然后把编译后的文件都放在`${basedir}/target/classes`

#### 编译测试源文件并运行

```powershell
mvn test
```

编译测试所需要的插件和依赖，然后执行测试

```
mvn test-compile
```

如果只想要简单的编译，执行如上

### 创建JAR文件

```shell
mvn package
```

创建后的JAR文件在`${basedir}/target`中

```
mvn install
```

如果想要把生成的JAR文件放到本地仓库中，就需要用到`mvn install`命令

> 这个跟package的区别是不仅他会把生成的JAR文件放到`${basedir}/target`中，也会把该生成的JAR文件放入到仓库中，相当于更新了JAR仓库。这样后面需要依赖整个JAR文件的项目就可以直接使用啦

### 清除

```
mvn clean
```

这个命令会清除`target`目录

### 构建

`build`其实就是`mvn compile`命令，只不过，构建是整个项目的`compile`，而`mvn compile`则是编译一部分更新的代码。

### 直接生成网站

```
mvn site
```

这个命令会执行一系列的子命令，以达到生成一个网站的目标

比如`mvn site`包括了validate、compile、test、package、verify、install等等一系列操作

<hr>

### 过滤资源文件

如果想要在资源文件中使用只有构建时才会生成的属性值，这些属性值可能在`pom.xml`、` user's settings.xml`、 `external properties file`或者在  `system property file`。

前提需要在`pom.xml`中设置`filtering`为true

属性内容一律使用`${}`的格式

完成后执行`mvn process-resources`就可以了，该命令是在构建生命周期阶段资源的复制和过滤。

如果直接在命令行定义属性值，则用`-D`参数，如下

```
mvn process-resources "-Dcommand.line.prop=hello again"
```

具体操作内容请参考[这里](https://maven.apache.org/guides/getting-started/index.html#How_do_I_setup_Maven)

还有如下章节可了解

1.使用外部依赖

2.在远程仓库部署jar

3.构建其它类型的项目

4.创建文档

5.同时构建多个项目

### maven的生命周期

#### 阶段

**clean**- 用于清除之前构建生成的所有文件

**validate**- 验证项目的正确性以及所有必要的信息是否可用

**compile**- 编译项目源代码

**test**- 测试编译的测试代码

**package**- 对编译的代码进行打包并发布

**verify**- 对集成测试进行检查以保证满足质量标准

**install**- 安装包到本地仓库，用于本地其他项目的依赖

**deploy**- 在构建环境中，将最终打包的文件复制到远程仓库当中用于和其他开发者和项目进行

有三个生命周期**，分别是：**clean、site、default**，他们都包含上面的**多个阶段构成，其实就是把多个阶段的周期绑定在一起

详细内容可参考[这里](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)

## Maven Plugin

其实maven的命令都是由插件来完成的，像编译是通过`maven-compile-plugin`实现的、测试是通过`maven-surefire-plugin`实现的，只是我们没有发觉罢了

实际情况是将插件目标与生命周期阶段（lifecycle phase）绑定，这样用户在命令行只是输入生命周期阶段而已，例如Maven默认将maven-compiler-plugin的compile目标与compile生命周期阶段绑定，因此命令`mvn compile`实际上是先定位到compile这一生命周期阶段，然后再根据绑定关系调用maven-compiler-plugin的compile目标。

详细内容请参考[这里](https://www.jianshu.com/p/741d9c69a6a4)，这里面也包括Maven命令的详细描述。

### SpringBoot Plugin 

Maven plugin的配置许多都是通过继承**Parent POM**来默认配置的，具体可参考[这里](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#using)，同时他还提供了许多用户属性，比如以`spring-boot`开头，可以从命令行自定义配置

```powershell
$ mvn spring-boot:run -Dspring-boot.run.profiles=dev,local
# 当运行程序的时候，调用某个profiles开启
```

还有其他的内容选项

| Goal                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [spring-boot:build-image](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-build-image) | Package an application into a OCI image using a buildpack.   |
| [spring-boot:build-info](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-build-info) | Generate a `build-info.properties` file based the content of the current `MavenProject`. |
| [spring-boot:help](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-help) | Display help information on spring-boot-maven-plugin. Call `mvn spring-boot:help -Ddetail=true -Dgoal=` to display parameter details. |
| [spring-boot:repackage](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-repackage) | Repackage existing JAR and WAR archives so that they can be executed from the command line using `java -jar`. With `layout=NONE` can also be used simply to package a JAR with nested dependencies (and no main class, so not executable). |
| [spring-boot:run](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-run) | Run an application in place.                                 |
| [spring-boot:start](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-start) | Start a spring application. Contrary to the `run` goal, this does not block and allows other goals to operate on the application. This goal is typically used in integration test scenario where the application is started before a test suite and stopped after. |
| [spring-boot:stop](https://docs.spring.io/spring-boot/docs/2.4.1/maven-plugin/reference/htmlsingle/#goals-stop) | Stop an application that has been started by the "start" goal. Typically invoked once a test suite has completed. |