### conda是一个包管理、环境管理工具:gear:

一般，miniconda只针对python:snake:这个环境，而anaconda其实就比较全面，可以用于多个编程语言的环境管理，比如R语言，JavaScript等等

<!--more-->

```
安装路径：/home/xxx/miniconda3

退出命令：conda deactivate env_name

删除环境：conda env remove env_name

显示环境列表：conda env list

如果创建环境，使用指定路径的方式，命令如下

conda create -p=path/env_name python=python_version

那么开启这个环境的方法：conda activate path/env_name

同理，删除这个环境的方法就是conda env remove -p /path/env_name
```

这样的话，该环境就指定了特定的路径，但是名称却为空，暂时还找不到如何改名字的方法，也就是说，在创建环境的时候，要么指定路径，要么指定名称（路径规定只在path/miniconda/env中）

:boom:解决方法:是使用{% lebal success@软链接 %}把路径引入path/miniconda/env/env_name中，就可以了，like the picture show below.

![image-20200316111210232](F:%5CAA_LLJ%5CGitRepository%5CDailyNote%5CComputerBasic%5CLinux%5Cimage-20200316111210232.png)

<hr>

有时候，使用python无法下载某些包，或者下载速度很慢，这是因为conda下载包是使用国外的网址来下载的，可以修改配置，使用国内的镜像

清华:man_student:镜像内容和修改方法在[这里](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

