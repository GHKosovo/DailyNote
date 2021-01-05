## ![640px-MySQL_logo.svg](C:\Users\llj\Documents\typero图像\640px-MySQL_logo.svg.png)

这里的mysql用法分linux和window两个部分，同时也采集了一些操作mysql时遇到的问题，放在结尾部分:card_index_dividers:

<!--more-->

<hr>

## linux上的mysql

#### 操作mysql服务:mushroom:

```sh
#查看mysql服务的状态
sudo service mysql status

#关闭mysql服务
sudo service mysql stop

#开启mysql服务
sudo service mysql start 
```

#### 查找mysql文件:sunflower:

```sh
#查询mysql的内容的地址(比较详细)
find / -name mysql  
#也可通过whereis(比较笼统)
whereis mysql
```

#### 查看安装包:herb:

```sh
#可通过如下命令，查询通过apt-get的已安装的软件
sudo dpkg -l
```

#### 数据的各种目录解析:seedling:

```sh
#mysql的datadir,用于存储数据
/var/lib/mysql

#mysql的配置文件目录
/etc/mysql

#mysql服务的目录，就是‘service mysql start’等命令的执行处
/etc/init.d/mysql

#其实很多文件都在/usr目录中
#mysql命令目录
/usr/bin/mysql

#动态库
/usr/lib/mysql

#帮助文档
/usr/share/mysql

```

<hr>
## window上的mysql


```sh
#开启服务
net start mysql
#关闭服务
net stop mysql
--------------------
(需要登录到mysql后才使用)
#查看数据库基本信息
status;
#查看端口号
show global variables like 'port';  
```

<hr>

## mysql问题集

#### 1.程序被占用？sock lock file无法修改？

```sh
2020-03-27T06:51:55.089835Z 0 [ERROR] Another process with pid 5244 is using unix socket file.
2020-03-27T06:51:55.089873Z 0 [ERROR] Unable to setup unix socket lock file.
2020-03-27T06:51:55.089886Z 0 [ERROR] Aborting
```

<span class="ljspan ljspan-red">解决办法</span>:

​	:one:：关闭mysql服务;

​	:two:：到/var/run/mysqld/目录中，找到mysqld.sock.lock文件,备份它并重命名或者备份后删除原文件

​	:three:：重新启动mysql服务即可	

#### 2.绑定IP/端口权限限制

```sh
2020-03-26T06:52:46.063437Z 0 [ERROR] Can't start server: Bind on TCP/IP port: Permission denied
2020-03-26T06:52:46.063462Z 0 [ERROR] Do you already have another mysqld server running on port: 3306 ?
2020-03-26T06:52:46.063487Z 0 [ERROR] Aborting
```

>遇到这个问题可能是端口占用问题，也可能是主机IP的设置问题

情况一：

我无法通过ssh方式远程登录mysql，但使用localhost本机，就可以登陆了，这个就是IP问题啦:pencil:

可通过修改mysql配置文件解决，可能是{% label danger@my.cnf %}文件，也有可能是{% warning@mysqld.cnf %}等等，反正看到有配置{% label info@datadir %}和{% label info@socket %}等字眼的文件就是了

进入{% label danger@my.cnf %}然后如果看到`bind-address   = 127.0.0.1`，就注销掉，这样就可以远程开启mysql服务了

情况二：
由于端口3306被占用，所以开启不了！kill掉占用这个端口的进程就行了

#### 3.mysql字符格式导致无法导出数据

想使用workbench{% label success@导出数据库表为sql脚本 %}，然后导入到其他系统中去
但是在使用sql脚本直接导入到linux中的mysql数据库中，但是一直报错，如下:point_down:

```sh
ERROR 1273 (HY000): Unknown collation: 'utf8mb4_0900_ai_ci'
```

很明显是字符错误的问题（排序规则），所以我改了数据库的排序规则，但是不行:dizzy_face:

点开每个数据库表，发现它的排序规则也不同，接着修改，还是不行，很奇怪:older_man:

后面发现每个column的排序规则也要修改，:turtle::turtle:，做完这一步，才能实现导入成功

> sql脚本记录的是表数据，并不是整个数据库，所以一般需要新建

```shell
#进入数据库
mysql -u username -p password
#进入数据库（没有需要新建）
use database;
#跑sql脚本
source /path/to/xxx.sql
```

#### 4.多个数据库混合使用同一个配置文件

使用<span class="ljspan ljspan-blue">sqlyog</span>登录数据库一直失败,但是使用cmd登录mysql却成功了:bow:
然后我就进入mysql中查看数据库的版本信息和端口号的信息,发现版本对了，但是端口错了，修改端口重启，但是端口还是没有变！(⊙o⊙)？......阿这......
找到问题了，原来因为我{% label danger@下载安装了多个数据库 %}又没有配置清楚，所以导致我登录的数据库使用到的是其他数据库的配置文件my.ini，这就导致了之前的修改端口无效。

问题找到了，但是这个执行文件的默认读取配置文件路径怎么改呢？....(⊙﹏⊙)

> 通过上网可知，可通过修改注册表来设置。

打开`注册表编辑器`--`HKEY_LOCAL_MACHINE`--`SYSTEM`--`CurrentControlSet`--`Services`(到这一步可以看到数据库名称)，之前我就是通过在这里修改`DisplayName`/`ImagePath`来区分不同的数据库服务的。现在可通过修改`ImagePath`，在里面的执行文件后添加
 --defaults-file="path\to\your mysql file\my.ini"（这是我在mysql8.0的服务中看到的:happy:）
这样就可以啦

#### 5.误用其他数据库配置导致无法读取数据

由于上一个问题导致了这一个问题(+_+)?

{% note info %}

在linux上可以通过日志目录直接查看日志；那windows上的日志文件应该也有吧，没有日志目录，但是在data文件中，其实有关于数据库增删改查的缓存和错误信息的日志，所以如果数据库无法连接，可以在这里找错误日志文件

{% endnote %}

这次的问题是因为之前mysql8.0使用mysql5.7的配置文件my.ini，而mysql5.7的{% label info@数据配置 %}都是在my.ini中的，所以导致之前{% label warning@mysql5.7操作数据库 %}使用的是{% label danger@mysql8.0的引擎 %}，而现在换成mysql.5.7引擎了,自然内部的缓存数据文件无法操作。

<span class="ljspan ljspan-red">解决方法</span>
1.直接重装数据库（如果里面没有重要数据）
2.删掉所有数据文件后重启数据库
3.通过查看错误日志文件，官方推荐是使用[强制恢复模式](https://dev.mysql.com/doc/refman/5.7/en/forcing-innodb-recovery.html)来恢复数据

#### 6.mysql初始化失败

想在电脑上安装mysql5.7，之前已经在我的电脑上装了两个mysql8，一个是源码安装，一个是安装器安装

这一次我也想通过源码安装，mysql服务安装倒是没问题，但是mysql初始化一直失败，不知道为何:hear_no_evil:

通过一些蛛丝马迹，我发现，每次初始化，我的另一个msyql的data文件夹有更新，但是我没有动它呀！(￣ ‘i ￣;)

查看日志，发现错误如下:point_down:

```sh
[Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
[Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
[Error] initialize specified but the data directory has files in it. Aborting
```

我还没初始化，我的mysql5.7目录下，并没有data文件夹，为何会说我有？

难道每次初始化都去mysql8文件夹下查找:grey_question:(⊙o⊙):grey_question:

既然如此，我觉得应该就是{% label success@环境变量 %}的问题了，所以我修改mysql的环境变量为mysql5.7的，重新执行如下就可以了

```mysql
#可以初始化了
mysql --initialize  
```

