用于练习Mysql的练习题,链接在[这里](https://blog.csdn.net/flycat296/article/details/63681089?spm=a2c4e.10696291.0.0.20b419a4SIu0Ty)

第九题解法：

```sql
select * from Student 
where Sid in( select Sid from SC 
where Sid not in (select Sid from SC 
where Cid not in (select Cid from SC 
where Sid = '01')) group by Sid having count(*) = (select count(Cid) from SC 
where Sid = '01') and Sid != '01');
```

第四十一题到四十五题：都是使用了时间函数/日期函数等，故不做锻炼，因为各个数据库对于这些函数的写法、用法和格式都不同