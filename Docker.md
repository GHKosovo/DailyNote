#### 镜像操作

检索：docker search 关键字

拉取：docker pull 镜像名:tag

列表：docker images

删除：docker rmi image-id

#### 容器操作

创建容器并启动：docker run --name -container-name -d -image-name

> docker run要区别于docker start
>
> docker run其实集合了两条命令：docker create 和 docker start

（`-d`：后台运行;`-p`: -host-port:-container-port：端口映射; `-it`  运行交互模式的容器，`-i`是以交互模式运行容器，而`-t`则是为容器分配一个伪输入终端，`-i`和`-t`经常一起使用; `-P` 将所有的公开端口映射到随机端口上）

> docker run是docker里面使用最多的命令，后台和前台模式并不是一直都通用的的，比如nginx就是一个后台app，所以就不能把nginx以前台模式运行，而“/bin/bash”这个应用是交互式，也就是前台模式，所以不能用后台模式运行。
>
> docker run 和docker exec 在打开时都可以启用一个应用，映射的接口就是映射到这个应用上，如果是sshd应用记得写正确了，不要用成/bin/bash哦

启动容器：docker start -container-id/-container-name

查看运行中的容器：docker ps (加-a，表示查看所以的容器)

停止运行中的容器：docker stop -container-id/-container-name

使用dockerfile或者上下文构建镜像：docker build path/url

> 上下文可以是指定路径或者链接的一个文件集

删除容器：docker rm -container-id

查看容器日志：docker logs -container-id/-container-name

进入容器：docker exec -it -container-id/container-name /bin/bash

设置docker容器自启动:

- 如果还没有创建容器
  docker run --restart=always --name=容器名
- 如果已经创建了容器
  docker update --restart=always 容器名