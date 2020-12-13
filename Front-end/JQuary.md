1.使用JQuary时，如果遇到需要查询某个属性的便签可以这么写


```
<select>
	<option selected>1</option>
	<option>2</option>
	<option>3</option>
</select>
```
$("option.selected")就是表示选中项的那个对象

2.find()的使用，find()函数用来查询对象内的子标签，如果不是子标签就没有必要使用它；

