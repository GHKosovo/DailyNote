## #前言

apt-get是ubuntu等系统独有的一个软件包安装工具，apt其实也是一样的，只不过，我习惯用apt-get。不适用于centos哦！:information_desk_person:

<!--more-->

## 命令：

> 下列是apt-get的命令选项

```sh
update - 取回更新的软件包列表信息     **#只检查更新软件包，不更新软件**

upgrade - 进行一次升级   **#真正的更新软件**

install - 安装新的软件包(注：软件包名称是 libc6 而非 libc6.deb)

remove - 卸载软件包

autoremove - 卸载所有自动安装且不再使用的软件包

purge - 卸载并清除软件包的配置

source - 下载源码包文件

build-dep - 为源码包配置所需的编译依赖关系

dist-upgrade - 发布版升级，见 apt-get(8)

dselect-upgrade - 根据 dselect 的选择来进行升级

clean - 删除所有已下载的包文件

autoclean - 删除已下载的旧包文件

check - 核对以确认系统的依赖关系的完整性

changelog - 下载指定软件包，并显示其changelog

download - 下载指定的二进制包到当前目录
```

<hr>

## 针对apt-get安装软件失败，网速慢的问题

> 可通过更换apt-get镜像源来解决这个问题

```sh
#备份原始镜像源

cp /etc/apt/source.list  /etc/apt/source.list.old

#更换镜像源地址

vim /etc/apt/source.list

:%d            #清空内容

#复制[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)到/etc/apt/source.list文件中

#更新一下配置

sudo apt-get update
```

<hr>

## apt-get安装程序包出现问题（卸载系统自带python3后出现的问题）

```sh
#以下为错误内容，可能有多个或至少一个
xxx.postinst: xxx: not found
dpkg: 处理软件包 xxx (--configure)时出错:
 子进程 已安装 post-installation 脚本 返回错误状态 127
在处理时有错误发生:
	xxx
E:Sub-process /usr/bin/dpkg returned an error code (1)

#解决办法：
sudo mv /var/lib/dpkg/info /var/lib/dpkg/info_old

sudo mkdir /var/lib/dpkg/info

#更新软件包并修复软件的依赖包
sudo apt-get update && sudo apt-get -f install

sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/info_old

sudo rm -rf /var/lib/dpkg/info 

sudo mv /var/lib/dpkg/info_old /var/lib/dpkg/info

#重新进入shell
```

>最好不要卸载系统自带的python，最多卸载你之前使用apt-get安装的软件，不然可能会出现意想不到的问题:cold_sweat:

## 卸载软件/程序

> 我觉得也可以用来删除其他通过apt-get安装的软件！

#### 示例：完全卸载mariadb

```sh
#首先卸载mariadb并删除相关配置文件(datadir目录)
sudo apt-get purge mariadb*

#删除没用的相关依赖包
sudo apt-get automove 

#验证mariadb服务是否还在
sudo service mysql status

#如果还是不能够删除!!!
#就直接到apt软件包列表去删除
/var/lib/dpkg/info
```



