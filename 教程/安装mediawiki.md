## #前言

安装<span class="ljspan ljspan-reverse ljspan-yellow">[mediawiki](https://www.mediawiki.org/wiki/MediaWiki)</span>很简单:grinning:，比较难的是需要安装她所需要的软件和配置，我在ubuntu安装过几个，都成功了，但是在centos上安装时，发现web服务器和<span class="ljspan ljspan-reverse ljspan-yellow">[mediawiki](https://www.mediawiki.org/wiki/MediaWiki)</span>出现某些冲突，导致无法使用，具体什么问题，我也不清楚，可能是我的web服务器配置出问题.....

## 安装

具体安装教程看[官网](https://www.mediawiki.org/wiki/MediaWiki)啦，得{% label danger@翻墙 %}哦，随让<span class="ljspan ljspan-yellow">[github](https://github.com/wikimedia/mediawiki)</span>上面也提供下载，但是没有教程阿，而且遇到问题，也没有给出解决办法，还是去[官网](https://www.mediawiki.org/wiki/MediaWiki)吧

如果提示说需要安装composer，[官网](https://www.mediawiki.org/wiki/MediaWiki)也有提供下载安装的方法

然后用composer安装一些依赖，如果这时候无法安装，出现内存不够（重启系统）或下载失败（修改composer镜像），这些都可以通过百度一一解决；但是修改composer镜像比较麻烦:hankey:

在修改镜像过程中，如果提示无法修改，出现类似问题：**Could not read /home/用户名/.composer/config.json**

> [这里](https://developer.aliyun.com/composer)是阿里云提供的修改镜像的方法

那么就修改一下`/home/用户名/.composer/config.json`和`/home/用户名/.composer/auth.json`的权限为777再重新运行composer 就应该可以了

<hr>

## mediawiki语法

1. __mediawiki__设置模板时，直接在搜索框输入“*Template:模板名*”就可以创建模板了，__调用模板__的方法为:[[Template:模板名]];
2. 修改皮肤的样式可通过在搜索中输入__MediaWiki:Vector.css__来找到对应的皮肤css样式文件后，修改整个皮肤的样式
3. 也可以通过在搜索框中输入__MediaWiki:Common.css__进入修改页面来设置全局的样式；
4. {{{1}}}表示参数，模板1引用参数可以使用{{模板2|参数名=参数}}来引入；
5. 在__mediawiki__中[[image/file]]表示链接:link:
6. **{{#while}}**和**{{#vardefine}}**等都是**解析器函数**，[参考文档在这里]([https://www.huijiwiki.com/wiki/%E5%B8%AE%E5%8A%A9:%E8%A7%A3%E6%9E%90%E5%99%A8%E5%87%BD%E6%95%B0#var.E7.B3.BB.E5.88.97.E5.87.BD.E6.95.B0](https://www.huijiwiki.com/wiki/帮助:解析器函数#var.E7.B3.BB.E5.88.97.E5.87.BD.E6.95.B0))
7. __vertical-align__元素是设置垂直对齐属性的，有baseline/top/middle/bottom/text-top/text-bottom等。

<hr>

## 重装mediawiki

### **如果只是想要升级**

那么简单，步骤如下:feet:

1. 输入网址：`http://ip:端口/项目/mw-config`
2. 在LocalSettings.php中查找一个wgUpgrade的参数，然后把它复制后填入网页中，就可以了

### 如果想要连数据库等重要配置都重置

把项目的{% label warning@LocalSettings.php %}文件给删了

输入网址：`http://ip:端口/项目/mw-config`

根据指引来配置项目，配置数据库的前提是提前配置好mysql数据库

配置数据库的需求：

- 数据库的名称

- 对应数据库指定的用户名和密码