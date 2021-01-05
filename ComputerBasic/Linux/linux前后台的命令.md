1.加在一个命令的最后，可以把这个命令放到{% label primary@后台执行 %}：**&**

> eg: `sh run.sh &`

<!--more-->

2.可以将一个正在前台执行的{% label danger@命令放到后台，并处于暂停状态 %}：**ctrl+z**

3.{% label danger@查看当前有多少程序在后台运行 %}的命令：**jobs**

```jobs -l```选项可显示所有任务的PID，jobs的状态可以是running, stopped, Terminated。但是如果任务被终止了（kill），shell 从当前的shell环境已知的列表中删除任务的进程标识

4.将{% label danger@后台的命令调至前台继续运行%}：**fg**

> eg：```fg %jobsnumber```

5.{% label danger@将后台暂停的命令，变成在后台继续执行 %}：**bg**

> eg：```bg %jobsnumber```

6.{% label danger@杀死进程 %}：**kill**

法子1：通过jobs命令查看job号（假设为num），然后执行`kill %num`
法子2：通过ps命令查看job的进程号（PID，假设为pid），然后执行`kill -s 9 pid`

7.{% label danger@前台进程的终止 %}：**Ctrl+c**

8.{% label warning@让程序/进程在后台进行，即使关闭终端也在执行 %}：**nohub**

如果让程序始终在后台执行，即使关闭当前的终端也执行（之前的&做不到），这时候需要nohup。该命令可以在你退出帐户/关闭终端之后继续运行相应的进程。关闭中断后，在另一个终端jobs已经无法看到后台跑得程序了，此时利用ps（进程查看命令）

9.{% label success@进程查看命令 %}：**ps**

#查看执行的test.sh命令

> ps -aux | grep “test.sh” 

#查看tomcat进程

> ps -aux |grep tomcat       

```
a:显示所有程序 
u:以用户为主的格式来显示 
x:显示所有程序，不以终端机来区分
```

