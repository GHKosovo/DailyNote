## #前言

在安装某些软件的时候经常会使用到这些指令，就是<span class="ljspan  ljspan-reverse ljspan-red">./configure</span>、<span class="ljspan ljspan-reverse ljspan-blue">make</span>、<span class="ljspan ljspan-reverse ljspan-yellow">make install</span>

我一直是不知道为何要执行这些指令的，一般不都是install不就完事了吗？

其实这里面大有文章啦:sunglasses:,这些就是所谓的自动化构建工具啦<!--more-->

{% note info %}

一般通过源码安装才会用到这些东西啦。

{% endnote %}

>那configure文件又是怎么来的呢？其实他还不是真正的源码，真正的源码通过autoconf和automake构建产生了configure文件。这方面就比较难了，详情可查看[这里](https://www.ibm.com/developerworks/cn/linux/l-makefile/)

<hr>

## configure

<span class="ljspan ljspan-reverse ljspan-red">configure</span>其实是一个可执行的脚本，它是用来生成<span class="ljspan ljspan-blue">makefile</span>的，<span class="ljspan ljspan-blue">makefile</span>是用来干嘛的呢？他就是安装的未编译脚本啦，所以在生成它时，可以通过<span class="ljspan ljspan-reverse ljspan-red">configure</span>的选项，来设置安装位置等

<hr>

## make

<span class="ljspan ljspan-reverse ljspan-blue">make</span>是用来编译由<span class="ljspan ljspan-reverse ljspan-red">configure</span>产生的<span class="ljspan ljspan-blue">makefile</span>文件的，编译后就生成了所需要的安装文件

<hr>

## make install

通过<span class="ljspan ljspan-blue">makefile</span>文件来安装程序

<hr>

## 其他命令

### make clean

清除编译产生的可执行文件及目标文件(object file，*.o)；其实我觉得这里说的不是很对，一切以<span class="ljspan ljspan-blue">makefile</span>里面所写内容为主。

### make check/make test

一般在make install命令之前执行的命令，用于检测编译文件时候正确。