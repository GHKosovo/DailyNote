#### 镜像操作

检索：docker search 关键字

拉取：docker pull 镜像名:tag

列表：docker images

删除：docker rmi image-id

#### 容器操作

根据镜像启动容器：docker run --name -container-name -d -image-name

（-d：后台运行;-p: -host-port; -container-port：端口映射; -it  运行交互模式的容器; -P 将所有的公开端口映射到随机端口上）

查看运行中的容器：docker ps (加-a，表示查看所以的容器)

停止运行中的容器：docker stop -container-id/-container-name

启动容器：docker start -container-id/-container-name

删除容器：docker rm -container-id

查看容器日志：docker logs -container-id/-container-name

进入容器：docker exec -it -container-id/container-name /bin/bash

设置docker容器自启动:

- 如果还没有创建容器
  docker run --restart=always --name=容器名
- 如果已经创建了容器
  docker update --restart=always 容器名