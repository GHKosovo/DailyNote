如果表的主键是自增，所以实现方法可以用到`useGeneratedKeys`以及`keyProperty`这两个属性。

新增的xml如图:

![img](https://img-blog.csdnimg.cn/20200826215034126.png#pic_center)

这里要注意几个问题:
1.keyProperty中对应的值是实体类的属性，而不是数据库的字段。
2.添加该属性之后并非改变insert方法的返回值，也就是说，该方法还是**返回新增的结果**。而如果需要获取新增行的主键ID，直接使用传入的实体对象的主键对应属性的值。如下图:

![img](https://img-blog.csdnimg.cn/20200826215835683.png#pic_center)

也就是说刚开始传入的参数payment使用mybatis新增后，返回的还是这个payment，只不过它拥有了主键id。

