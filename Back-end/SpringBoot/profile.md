## springboot的profile设置方法

### 法一：

建立多个配置文件；以`application-{}.properties`或`application-{}.yml`为形式的配置文件都是一个个`profile`，可以在`{}`中填入test,dev,prod等，其中，application.yml或application.properties可以启用其他配置文件

### 法二：

在`application.yml`文件中以`---`为间隔，springboot.2.4.x版本已经抛弃`spring.profiles`属性了，改为`spring.config.on-profile.`，所以只要各profile的属性不多，可以都写在同一个application.yml中；最后记得激活一个：`spring.profiles.active:xxx`

### 法三：

打包好以后的的文件可以使用java命令行来配置文件，运行命令行可以通过配置参数来设置启用哪个profile

```sh
java -jar xxx.jar --spring.profiles.active=xxx
```

### 其他方法：

在idea运行配置里面可以通过设置三个地方的其中之一就可以启用某个profile

- VM options设置启动参数  `-Dspring.profiles.active=prod`
- Program arguments设置  `--spring.profiles.active=prod`
- Active Profile 设置  `prod`

这三个参数不要一起设置，会引起冲突，选一种即可

