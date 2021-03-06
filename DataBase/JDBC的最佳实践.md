### JDBC是Java为多种关系型数据库提供的统一的访问接口

 

**1. 使用PrearedStatement**

任何一个使用过JDBC的Java程序员几乎都知道这个，PreparedStatment可以通过预编译的方式避免我们在拼接SQL时造成SQL注入。

 

**2. 使用ConnectionPool（连接池）** 

使用连接池作为最佳实践几乎都成了公认的标准。一些框架已经提供了内建的连接池支持， 例如Spring中的Database Connection Pool，如果你的应用部署在JavaEE的应用服务器中， 例如JBoss，WAS，这些服务器也会有内建的连接池支持，例如DBCP。 使用连接的原因简单的说就是因为创建JDBC连接耗时比较长，如果每次查询都重新打开一个连接， 然后关闭，性能将会非常低，而如果事先创建好一批连接缓存起来，使用的时候取出， 不使用的时候仍不关闭，将会节省大量的创建关闭连接的时间。

 

**3. 禁用自动提交**

这个最佳实践在我们使用JDBC的批量提交的时候显得非常有用，将自动提交禁用后， 你可以将一组数据库操作放在一个事务中，而自动提交模式每次执行SQL语句都将执行自己的事务， 并且在执行结束提交。

 

**4. 使用Batch Update**

JDBC的API提供了通过addBatch()方法向batch中添加SQL查询，然后通过executeBatch()执行批量的查询。 JDBC batch update可以减少数据库数据传输的往返次数，从而提高性能。

 

**5. 使用列名获取ResultSet中的数据，从而避免invalidColumIndexError**

JDBC中的查询结果封装在ResultSet中，我们可以通过列名和列序号两种方 式获取查询的数据， 当我们传入的列序号不正确的时候，就会抛出invalidColumIndexException， 例如你传入了0，就会出错，因为ResultSet中的列序号是从1开始的。 另外，如果你更改了数据表中列的顺序，你也不必更改JDBC代码，保持了程序的健壮性。 有一些Java程序员 可能会说通过序号访问列要比列名访问快一些，确实是这样，但是为了程序的健壮性、可读性，我还是更推荐你使用列名来访问。

 

**6. 使用变量绑定而不是字符串拼接**

在第一条最佳实践中，我们已经说过要使用PreparedStatment可以防止注入，而使用？ 或者其他占位符也会提升性能，因为这样数据库就可以使用不同的参数执行相同的查询， 这个最佳实践带来更高的性能的同时也防止了SQL注入。



 **7.在finally块中关闭资源，保证即使出了异常也能正常关闭**



**8. 要记住关闭ResultSet、Statement、PreparedStatement和Connection**

通常的做法是在finally块中关闭它们，这样做的好处是不论语句执行正确与否， 不管是否有异常抛出，都能保证资源被释放。在Java7中，可以通过Automatic Resource Management Block来自动的关闭资源。

如果没有关闭的ResultSet和Statement就会造成资源泄漏了。



**9.数据库的隔离级别越高性能越差，确保你的数据库连接设置的隔离级别是最优的**

 

