## 拷贝数据

从原来的mysql数据库datadir目录，cp出所有东西，其中最重要的，除了那些数据库数据表，还有ibdata1这个文件，和iblogfile0、ib_logfile1和ib_buffer_pool等文件，最好把整个目录都拷贝下来。{% label info@属性和用户权限 %}都要一并复制哦:open_mouth:

<!--more-->

在新的系统中新装一个mysql，我使用{% label success@APT安装模式 %}，所以目录配置都自动帮我配置好了！:slightly_smiling_face:

## 卸载mariadb

```sh
#首先卸载mariadb并删除相关配置文件(datadir目录)
sudo apt-get purge mariadb*

#删除没用的相关依赖包
sudo apt-get automove 

#验证mariadb服务是否还在
sudo service mysql status
```

## 重装mysql服务

```sh
#!!!把拷贝的datadir目录拷贝回去/var/lib/mysql
#datadir目录下一般包含ibdata1,iblogfile0,数据库目录等
sudo cp -rpf /path/to/datadir/* /var/lib/mysql/

#添加mysql-apt-仓库到系统仓库列表
sudo dpkg -i /PATH/version-specific-package-name.deb

#由于我是重装，之前已经配置了仓库，所以省略上一步
#从仓库更新包信息
sudo apt-get update

#安装mysql
sudo apt-get install mysql-server

#查看mysql服务端口3306是否被占用
ps -aux|grep mysql
#没有的话就可以了启动服务，有的话，用命令kill杀死那个进程

#启动mysql服务
sudo service mysql start

#测试一下是否正常启动
mysql --version
#看到以下版本信息就证明完成安装了！
mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

```

<hr>

## #忘记密码？

有办法更新root用户密码的:smile_cat:,别慌

如果root用户密码忘记，先关了mysql服务，然后到{% label warning@ my.cnf %}文件中做如下修改

```ini
[mysqld]#在这个下面
skip-grant-tables #添加一行

```

然后在系统中直接输入命令

> mysql -uroot    #就可以直接登录

```mysql
#在登录的时候更新密码
UPDATE mysql.user SET authentication_string=password('xxxxxx') WHERE User='root' AND Host ='localhost'; 

#刷新权限
flush privileges;

#退出
exit
```

{% note warning %}

注意!!!:boom::boom:

更改密码后，记得注释掉skip-grant-tables;

{% endnote %}
