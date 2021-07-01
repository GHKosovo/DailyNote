## 拼接URL

th:href构成的URL，直接用`@{/news/list/}` [绝对路径] ，那么url会映射成：

```html
http://域名或IP:端口 + 项目名 + /new/list/

比如,项目名为空，则如下：
http://localhost:8081/news/list/
```

如果去掉第一个斜杠，用 `@{news/list/}`，从以上的Controller路径点击，则url会映射成：

```
http://localhost:8081/news/list/ + /news/list
```

## 拼接URL带参数

```
<a th:href="@{/news/list/{type}(type=${type},page=${indexPage},size=${size})}"
```

括号里的表示括号左边被大括号包起来的变量应该取得值，这里：

- type会被后端传来的type变量值赋值，
- page会被后端传来的indexPage变量值赋值，
- size会被后端传来的size变量值赋值。

它们作用的结果：

1. 如果**括号前出现了对应的大括号变量**，映射时会替换掉，这里如果传来的type为"top"，则映射的url对应位置为`/news/list/top`
2. 如果**括号前没有出现对应的大括号变量**，则会将括号里对应的变量变为url参数形式，即`?page=1&size=10`

