## Python版本

1.分别有python和python3两种，python3对应Python3.x版本

而python对应Python2.x版本

2.pip和pip2相同，均对应Python2.x版本；而pip3对应Python3.x版本

<!--more-->

---

## Python包管理工具

现在的包管理工具都是用<span class="ljspan  ljspan-red">pip</span>了，以前都是使用<span class="ljspan ljspan-black">setuptools</span>，老版本的python就还都在用。

<hr>

## python虚拟环境

搭建python虚拟环境，现在都使用<span class="ljspan ljspan-red">conda</span>来安装

> 安装Conda，请看[这里]()

不过，以前都是用<span class="ljspan">virtualenv</span>来设置虚拟环境的

这里有安装步骤:point_down:

```sh
#[---]：大括号内的内容需要用户自己填写
#安装
pip install virtualenv

#创建目录，这是必须的，用于存储虚拟环境
mkdir myproject
cd myproject/

#创建一个独立的python环境（即虚拟环境）
virtualenv --python=[python-version] [env-name]

#进入这个虚拟环境
source [env-name]/bin/activate
```

<hr>

#### Python的其他工具

---

1. Fabric 是一个 Python (2.5-2.7) 的库和命令行工具，用来提高基于 SSH 的应用部署和系统管理效率。
2. 是用于自动化构建软件的工具：bulidout
3. 图像处理标准库：pillow

