1. No converter found for return value of type: class x.x.x.x(某某类)    这是因为java程序无法转化class到Jsp页面中，需要转化为json后才能接收
2. 在eclipse上，项目可以运行，但是使用debug的时候一直报超时，导致无法使用，删除tomcat/项目后重新部署，还是不行，删除所有断点后重启eclipse后就可以使用
3. debug又他么提示超时，不知道咋回事，我就四个断点啊，怎么还报错，我看到我把断点设在了方法上，把他取消掉就可以了，龟龟，坑太多了
4. 前端想要发送key-value的json对象到后台，但是不知道后台如何接收，String对象不行，map对象也不行，最后尝试再前端把json对象处理成json字符串后，后台使用@RequestParam映射这个字符串，就可以接收到了

