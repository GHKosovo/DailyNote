{% note info %}

mariadb和mysql等数据库，其实没有什么配置文件，就{% label warning@/etc/my.cnf %}，就算有其他配置文件，也都写在{% label info@my.cnf %}里面，从这里面找就行:smile:

{% endnote %}

## 修改配置文件

忘记<span class="ljspan ljspan-reverse ljspan-red">MariaDB</span>的root密码，需要修改root密码

在<span class="ljspan ljspan-reverse ljspan-red">MariaDB</span>配置文件{% label warning@/etc/my.cnf %}中的{% label success@[mysqld] %}中加入一行：`skip-grant-tables`

<!--more-->

```ini
[mysqld]
port=3307
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd
#取消mysql进行域名解析
skip-name-resolve
#设置数据去默认编码为UTF-8
character_set_server=utf8
init_connect='SET NAMES utf8'
#skip-grant-tables
#加入下面这一行
skip-grant-tables
```

重启服务

```
$ /etc/init.d/mysqld restart
```

## 重新登陆

进入<span class="ljspan ljspan-reverse ljspan-red">MariaDB</span>修改root的密码

```mysql
$ mysql -uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.27 MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| engine_cost               |
| event                     |
| func                      |
| general_log               |
| gtid_executed             |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| server_cost               |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
31 rows in set (0.00 sec)

mysql> update user set authentication_string=password('root') where user='root

Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1

mysql> flush privileges

Query OK, 0 rows affected (0.00 sec)

mysql> quit
Bye

```

>这里的authentication_string字段有时候可能是password，取决于你的版本
>
>如果在修改密码的时候出现这样的错误`Unknown column 'password' in 'field list'`，那证明mysql不支持passward字段了，mysql5.7以后，authentication_string字段代替了password字段

## 再次登陆

在登陆前要注释掉配置文件{% label warning@/etc/my.cnf %}中 {% label success@[mysqld] %}里面的的`skip-grant-tables`字段

```mysql
$ mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.27 MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

