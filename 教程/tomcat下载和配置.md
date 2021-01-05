## #前言

在Linux上安装tomcat，其实跟window一样，配置JDK、配置环境变量等等，只不过配置路径和文件名不同而已:kissing_heart:

<hr>

### 下载配置jdk

很难，试过用apt-get/apt使用ppa源安装，失败；使用系统推荐的openjdk安装也失败；最后只能采用下载bin来解压安装，才能成功

{% note default %}

#### Note:

在oracle官网下载jdk时，可以直接搜索jdk archive来下载，不过需要登陆oracle账号！

{% endnote %}

```commonlisp
Note:在oracle官网下载jdk时，可以直接搜索jdk archive来下载，不过需要登陆oracle账号！
```

> 安装好jdk需要配置好环境变量

如果只想配置用户变量，就在文件~/.bashrc中

```sh
export JAVA_HOME=/path/to/java

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib

export PATH=.:${JAVA_HOME}/bin:$PATH
```

如果想直接设置为环境变量那便把上面的设置添加到/etc/profile文件中

```sh
#测试java是否安装配置完成
$java -version
openjdk version "1.8.0_252"
OpenJDK Runtime Environment (build 1.8.0_252-8u252-b09-1~16.04-b09)
OpenJDK 64-Bit Server VM (build 25.252-b09, mixed mode)
#有上面的文段证明安装完成
```

<hr>

### 下载配置tomcat

配置教程在[这里](https://blog.csdn.net/jenyzhang/article/details/70159769)

```sh
tar -xzvf apache-tomcat.xxx.tar.gz
#配置环境变量
vim ~/.bashrc
#tomcat
export TOMCAT_HOME=/path/to/tomcat 

#启动tomcat
cd /path/to/tomcat
./bin/startup.sh

#测试tomcat配置成功与否,用浏览器登录下看看
http://localhost:8080
```



这里配置安装的tomcat还不是很完整，比如说，没有服务，每次开机都去tomcat文件夹去找，很麻烦，而且有些软件需要使用到systemctl来启动tomcat服务，比如OpenElis

解决办法：

```sh
cd /path/to/tomcat目录

#修改startup.sh

vim ./startup.sh
#添加如下
#set java environment
export JAVA_HOME=/path/to/java

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:$PATH

#tomcat
export TOMCAT_HOME=/path/to/tomcat
exec "$PRGDIR"/"$EXECUTABLE" start "$@"


#注册tomcat服务
sudo cp /path/to/tomcat/bin/catalina.sh /etc/init.d/tomcat
#修改catalina.sh
vim catalina.sh
#添加如下
JAVA_HOME=/usr/lib/jvm/java目录
CATALINA_HOME=/path/to/tomcat

在#!/bash/sh下方直接添加

### BEGIN INIT INFO

# Provides:          tomcat
# Required-Start:    $remote_fs $network
# Required-Stop:     $remote_fs $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: The tomcat Java Application Server

### END INIT INFO

#重新注册tomcat服务
sudo update-rc.d tomcat defaults

#现在就可以使用tomcat服务了
sudo systemctl status tomcat 
```

