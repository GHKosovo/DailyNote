在使用curl下载github内容时经常出现问题

比如：`curl: (7) Failed to connect to raw.githubusercontent.com port 443: Connection refused`

<!--more-->

这是因为{% label success@**raw.githubusercontent.com** %} 这个域名被污染了，所以在hosts文件里面添加一下ip映射就可以啦

```sh
$ vim /etc/hosts

#添加如下，ip可通过 https://www.ipaddress.com/ 查询 
x.x.x.x(ip) raw.githubusercontent.com

#这样就可以啦！
```

> 其实在window上也经常遇到类似问题:smirk:

 