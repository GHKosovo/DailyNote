## 关于Java语言中的包机制

1. 包又称为`package`,Java中引入`package`这种语法机制主要是为了方便程序的管理。不同功能的类被分门别类到不同的软件包中，查找比较方便，管理比较方便，易维护。

2. 怎么定义package呢？

   - 在Java源程序的第一行上编写`package`语句
   - package只能编写一个语句
   - 语法结构：`package` 包名；

3. 包名的命名规范：

   公司域名倒叙+项目名+模块名+功能名

   采用这种方式重名的几率较低。因为公司域名具有全球唯一性

   例如：

   ​	`com.liripo.wxproject.user.service`

   ​	`org.apache.tomcat.core`

4. 包名要求全部小写，包名也是标识符，必须遵守标识符的命名规则

5. 一个包对应的目录

6. 使用了`package`机制之后，应该怎么编译？怎么运行呢？

   - 使用了`package`机制之后，类名不再是`Test01`了，类名是：`com.liripo.wxproject.user.service`

   - 编译：`javac java源文件路径`（在硬盘上生成一个`class`文件）

   - 手动方式创建目录，将`Test01.class`字节码文件放到指定的目录下

   - 运行：`Java com.liripo.wxproject.user.service.Test01`

   - 另一种方式（编译+运行）：

     - 编译：`javac -d 编译之后存放的路径 java源文件的路径`

     - 例如：将`F：\Hello.java`文件编译之后放到`C:\`目录下

       `javac -d C:\ F:\Hello.java`

     - `javac -d . *.java`

       将当前路径中`*.java`编译之后存放到当前目录下

     - 运行：`Jvm`的类加载器`ClassLoader`默认从当前路径下加载。保证DOS命令窗口的路径先切换到com所在的路径。

## import

#### import语句用来完成导入其他类，同一个包下的类不需要导入，不在同一个包下需要手动导入。

```
import语法格式：

	import   类名；

	import   包名.*；
```

import语句需要编写到package语句之下，class语句之上

#### 最终结论

什么时候需要import?

- 不是`java.lang`包下，并且不在同一个包下的时候，需要使用`import`