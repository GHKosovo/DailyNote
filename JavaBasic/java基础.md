### 1、能直接新建一个接口？

当然不行，但是我明明看过，比如以下代码

```
Collections.sort(list,new Comparator<Student>(){
	@Override
	public int Compara(Student s1,Student s2){
		if (s1.age > s1.age()) {
            return 1;
        }
        if (s1.age < s2.age()) {
            return -1;
        }
        return 0;
	}
})
```

实际上，这是new 了一个实现了结构的**匿名类**，需要在匿名类内部实现那个接口