1.在查看Bootstrap官方文档时，对于[Navs](https://getbootstrap.com/docs/4.5/components/navs/)中使用nav-tab的情况，使得选项框的底部边框变没了，很好奇，所以就研究起来

![image-20201015214229503](C:\Users\llj\AppData\Roaming\Typora\typora-user-images\image-20201015214229503.png)

其实方法很简单，就是把底部边框变成白色后覆盖就可以了

但是CSS中使用 `style="margin-bottom: -1px;"`，我很好奇，所以就做了下分析

发现一个有趣的东西

![image-20201015214514319](C:\Users\llj\AppData\Roaming\Typora\typora-user-images\image-20201015214514319.png)

一般的DIV布局分布如上，有margin（外边界），border（边框），padding（内边界）

```html
<!--代码奉上-->
<div style="border-bottom: 1px solid #dee2e6;">
    	<!--使用了margin-bottom: -1px-->
		<div class="" style="margin-bottom: -1px;">
			<a class="btn " style="border-color: #dee2e6 #dee2e6 #fff;">鑫总</a>
		</div>
</div>
```

可以看到第一个DIV和a标签之间其实没有margin，而通过设置margin-bottom，使得a标签的border向外(下)移动了1px，这就导致第一个DIV和a标签的border重叠了

挺有趣的，一般的，在内部的标签，margin为正时，会让内部的内容缩小，而设置margin为正，就让内部的内容反而变大溢出外部标签的方框。