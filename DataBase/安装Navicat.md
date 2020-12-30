# Navicat无法连接到Docker上的mysql

Docker上装的是mysql 8.0

想用Navicat连接的时候，发现连接不上了，龟龟:turtle::turtle:

```mysql
Error：1251 - Client does not support authentication protocol requested by server; consider upgrading MySQL client.
```

网上大多数人说，是因为新的mysql的权限更改了和安全提升了，对密码使用了强加密的模式，所以导致第三方软件无法登陆mysql。

然后我根据大多数人的建议，进入docker容器中，进入mysql

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123';
mysql> FLUSH PRIVILEGES;
```

但是修改完了，我还是报上面1251的错误，实在是不知道如何搞，就重新装了低版本mysql 5.7的数据库，就可以连接成功了。

### 其实不用改版本

为何还报错，那是因为我用远程连接，上面的代码修改的是本机上的访问，通过查询数据库配置，如下

```mysql
mysql> SELECT Host,User,plugin from user;
+-----------+------------------+-----------------------+
| Host      | User             | plugin                |
+-----------+------------------+-----------------------+
| %         | luoj             | caching_sha2_password |
| %         | root             | caching_sha2_password |
| localhost | mysql.infoschema | caching_sha2_password |
| localhost | mysql.session    | caching_sha2_password |
| localhost | mysql.sys        | caching_sha2_password |
| localhost | root             | mysql_native_password |
+-----------+------------------+-----------------------+
6 rows in set (0.00 sec)
```

可知我只改了localhost上的插件，而我要改的是那个Host为`%`的，`%`代表任何主机。所以自然报错了

```mysql
mysql> ALTER USER 'root'@'%' IDENTIFIED BY 'lj123456' PASSWORD EXPIRE NEVER;
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'lj123456';
mysql> FLUSH PRIVILEGES;
```

通过如上的配置就可以啦

#### 遇到 "this is incompatible with sql_mode=only_full_group_by"错误

这个问题更大

通过网上网友的说法，发现是`select @@sql_mode;`，也就是sql_mode在搞鬼，然后我进去docker的mysql.5.7（mysql_02）中修改

`/etc/mysql/mysql.conf.d/mysqld.cnf `，添加了`sql_mode = STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION`;然后重启mysql，发现自动退出容器，后无法再登陆这个容器了，报错了。龟龟，现在连容器都进不去，如何修改阿，龟龟