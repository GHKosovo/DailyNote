## maven依赖问题

由于我的maven镜像配置是aliyun的，所以，我的所有依赖包都会去aliyun仓库去找，但是，一直找不到两个包

通过[aliyun仓库服务](https://maven.aliyun.com/mvn/search)，我确实找不到这两个包的依赖，所以自然无法自动下载

但是我的`pom.xml`文件里面也配备了仓库啊，阿里云里面没有，那么我的`pom.xml`配备的仓库应该有了吧

构建我的项目却还一直报缺少依赖的错误。

通过上网查看，原因是：

我在`/path/to/maven/config/settings.xml`中看到我的mirrors属性中配置如下:point_down:

```
 <mirror>
      <id>aliyun</id>
	  #这里的“*”表示只用这个仓库，其他仓库都不用，全部忽略
      <mirrorOf>*</mirrorOf>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
 </mirror>
```

由于这个原因，所以导致后面pom.xml配置的东西都被忽略了，所以才找不到那些依赖包:poop: