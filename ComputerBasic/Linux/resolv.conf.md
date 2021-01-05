## #前言

resolv.conf是什么呢:grey_question:，光看名字不懂！:dizzy:其实他就是一个DNS客户机配置文件，用于设置DNS服务器的IP地址以及DNS域名

## resolv.conf文件解析

```sh
nameserver    *//定义DNS服务器的IP地址* 
domain       *//定义本地域名* 
search        *//定义域名的搜索列表* 
sortlist        *//对返回的域名进行排序*
```
> nameserver参数必须有:grinning:，其他为可选

<hr>

## resolv.conf由三种方式控制

1.network manager

2.resolvconf

3.systemd-resolved

<hr>

## 关于避免在/etc/resolv.conf中使用namespace127.0.0.53

这个问题在<span class="ljspan ljspan-reverse ljspan-blue">[安装elabFTW](/2020/安装elabFTW/)</span>时出现过，因为elabftw是采用docker安装的，安装时，用到两个容器，分别是elabftw_web和mysql容器，但是两个容器一直无法连接，一直报没有安装mysql容器，最后通过<span class="ljspan ljspan-blue">[github提问](https://github.com/elabftw/elabftw/issues/1881)</span>,作者告诉了我问题的原因就是这个，提供的解决办法再上面的文章上，最后我是改用networkmanager来管理,才解决该问题

> 关于这个问题的详细叙述文章在[这里](https://www.hiroom2.com/2017/08/24/ubuntu-1610-nameserver-127-0-0-53-en/)