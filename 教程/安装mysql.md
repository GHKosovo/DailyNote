## 下载

1. 在<span class="ljspan ljspan-reverse">[mysql官网](https://dev.mysql.com/downloads/repo/apt/)</span>下载mysql的apt软件包:package:

   {% note primary %}

   Note:在官网下载时，如果采用复制链接下载，对于复制download的链接地址是错误的，需要点击进入，复制里面的“**[No thanks, just start my download.](https://dev.mysql.com/get/mysql-apt-config_0.8.15-1_all.deb)**”，这个才是真正的链接地址

   {% endnote %}

<!--more-->

2. 也可通过wget直接下载:slightly_smiling_face:

>比较简单，配置文件都自动设置好，包括设置环境变量等等

```sh
#下载
wget -c https://dev.mysql.com/get/mysql-apt-config_0.8.15-1_all.deb
#deb包的当前目录中，执行如下(记得替换w.x.y)
sudo dpkg -i mysql-apt-config_w.x.y-z_all.deb

#更新apt-get仓库
sudo apt-get update
```

### 开始正式安装

```sh
sudo apt-get install mysql-server

#安装过程中，弹出窗口两次设置密码
密码：lj123456(root用户)
```

### 然后结果出错了

```sh
llj@DESKTOP-6CFA5V6:~/Downloads$ sudo apt-get install mysql-server
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libaio1 libmecab2 mysql-client mysql-common mysql-community-client
  mysql-community-server
The following NEW packages will be installed:
  libaio1 libmecab2 mysql-client mysql-common mysql-community-client
  mysql-community-server mysql-server
0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
Need to get 52.9 MB of archives.
After this operation, 320 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/main amd64 mysql-common all 5.8+1.0.4 [7308 B]
Get:2 https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic-updates/main amd64 libaio1 amd64 0.3.110-5ubuntu0.1 [6476 B]
Get:3 http://repo.mysql.com/apt/ubuntu bionic/mysql-5.7 amd64 mysql-community-client amd64 5.7.29-1ubuntu18.04 [14.9 MB]
Get:4 https://mirrors.tuna.tsinghua.edu.cn/ubuntu bionic/universe amd64 libmecab2 amd64 0.996-5 [257 kB]
Get:5 http://repo.mysql.com/apt/ubuntu bionic/mysql-5.7 amd64 mysql-client amd64 5.7.29-1ubuntu18.04 [72.8 kB]
Get:6 http://repo.mysql.com/apt/ubuntu bionic/mysql-5.7 amd64 mysql-community-server amd64 5.7.29-1ubuntu18.04 [37.6 MB]
Get:7 http://repo.mysql.com/apt/ubuntu bionic/mysql-5.7 amd64 mysql-server amd64 5.7.29-1ubuntu18.04 [72.8 kB]
Fetched 52.9 MB in 48s (1091 kB/s)                                       
Preconfiguring packages ...
Selecting previously unselected package mysql-common.
(Reading database ... 35222 files and directories currently installed.)
Preparing to unpack .../0-mysql-common_5.8+1.0.4_all.deb ...
Unpacking mysql-common (5.8+1.0.4) ...
Selecting previously unselected package libaio1:amd64.
Preparing to unpack .../1-libaio1_0.3.110-5ubuntu0.1_amd64.deb ...
Unpacking libaio1:amd64 (0.3.110-5ubuntu0.1) ...
Selecting previously unselected package mysql-community-client.
Preparing to unpack .../2-mysql-community-client_5.7.29-1ubuntu18.04_amd64.deb ...
Unpacking mysql-community-client (5.7.29-1ubuntu18.04) ...
Selecting previously unselected package mysql-client.
Preparing to unpack .../3-mysql-client_5.7.29-1ubuntu18.04_amd64.deb ...
Unpacking mysql-client (5.7.29-1ubuntu18.04) ...
Selecting previously unselected package libmecab2:amd64.
Preparing to unpack .../4-libmecab2_0.996-5_amd64.deb ...
Unpacking libmecab2:amd64 (0.996-5) ...
Selecting previously unselected package mysql-community-server.
Preparing to unpack .../5-mysql-community-server_5.7.29-1ubuntu18.04_amd64.deb ...
Unpacking mysql-community-server (5.7.29-1ubuntu18.04) ...
Selecting previously unselected package mysql-server.
Preparing to unpack .../6-mysql-server_5.7.29-1ubuntu18.04_amd64.deb ...
Unpacking mysql-server (5.7.29-1ubuntu18.04) ...
Setting up mysql-common (5.8+1.0.4) ...
update-alternatives: using /etc/mysql/my.cnf.fallback to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Setting up libmecab2:amd64 (0.996-5) ...
Setting up libaio1:amd64 (0.3.110-5ubuntu0.1) ...
Setting up mysql-community-client (5.7.29-1ubuntu18.04) ...
Setting up mysql-client (5.7.29-1ubuntu18.04) ...
Setting up mysql-community-server (5.7.29-1ubuntu18.04) ...
update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode
dpkg: error processing package mysql-community-server (--configure):
 installed mysql-community-server package post-installation script subprocess returned error exit status 1
dpkg: dependency problems prevent configuration of mysql-server:
 mysql-server depends on mysql-community-server (= 5.7.29-1ubuntu18.04); however:
  Package mysql-community-server is not configured yet.

dpkg: error processing package mysql-server (--configure):
 dependency problems - leaving unconfigured
Processing triggers for libc-bin (2.27-3ubuntu1) ...
No apport report written because the error message indicates its a followup error from a previous failure.
                                Processing triggers for systemd (237-3ubuntu10.39) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Processing triggers for ureadahead (0.100.0-21) ...
Errors were encountered while processing:
 mysql-community-server
 mysql-server
E: Sub-process /usr/bin/dpkg returned an error code (1)
```
- 解决上面的问题的办法，具体发生的原因的还未知

## 错误:black_flag:

如遇到dpkg的错误，需要更新dpkg包

```sh
#在/var/lib/dpkg/下，拷贝info目录到info_old
sudo mv /var/lib/dpkg/inf /var/lib/dpkg/info_old 
#新建一个info目录
sudo mkdir /var/lib/dpkg/info
#更新apt-get包,并安装上文出现错误的安装包
sudo apt-get update 
sudo apt-get -f install mysql-community-server
然后移动所有新生成的文件包到info_old目录中
mv /var/lib/dpkg/info/* /var/lib/dpkg/info_old
#删除info目录，给info_old改名为info
sudo rm -rf /var/lib/dpkg/info
sudo mv /var/lib/dpkg/info_old /var/lib/dpkg/info

#重新执行一遍
sudo apt-get install mysql-server
#没什么问题
#但是没有自动启动msyql服务

```

{% note info %}

其实，一般mysql遇到问题，都可以在日志文件中找到，比如我遇到的一个问题...=_=''

{% endnote %}

```
2020-03-26T06:52:46.063437Z 0 [ERROR] Can't start server: Bind on TCP/IP port: Permission denied
2020-03-26T06:52:46.063462Z 0 [ERROR] Do you already have another mysqld server running on port: 3306 ?
2020-03-26T06:52:46.063487Z 0 [ERROR] Aborting
```

<span class="ljspan ljspan-red">解决办法</span>:

```sh
#原来我无法通过ssh方式登录，使用localhost本机，就可以登陆了，但是还有个错误，可通过查看my.cnf文件查看，也有可能是mysqld.cnf等等，反正看到有配置datadir和socket等字眼的文件就是了
#进入my.cnf然后如果看到bind-address   = 127.0.0.1，就注销掉，这样就可以远程开启mysql服务了

#查看占用3306端口的程序
ps -aux |grep mysql
#如过被占用就用命令kill杀死进程

#开启数据库服务
sudo service mysql start 

#使用root用户登录
mysql -uroot -p 

#修改root密码(这里是修改当前用户密码的用户，5.7.29指定的方式，使用update/set都不行)
alter user user() identified by '123456';
```

原来我无法通过ssh方式登录，使用localhost本机，就可以登陆了，但是还有个错误，可是不受影响

```sh
2020-03-26T07:17:06.226584Z 0 [ERROR] InnoDB: Linux Native AIO interface is not supported on this platform. Please check your OS documentation and install appropriate binary of InnoDB.
```

