# Mysql相关学习



## 1、Mysql 的相关操作

###  1.1、连接 mysql

```mysql
mysql -h主机地址 -u用户名 －p用户密码
```



## 2、Mysql 的导出和导入

> mysql 的导出，一般的程序员会使用工具进行导入和导出。但是随着你的开发年龄，使用 mysql 原生的导入导出的技能的必须要的。

- 只导出结构，不导出数据：

> 导出表结构却不导出表数据——只返回特定数据库特定表格的表格结构，不返回数据,添加“-d”命令参数

```mysql
mysqldump -hlocalhost -uroot -P3306 -p6NbAFQBE -d btmox>./btmox.sql
mysqldump -hlocalhost -uroot -P3306 -p6NbAFQBE -d mxhy>./mxhy.sql
```

- 只导出数据，不导出结构：

> 导出数据却不导出表结构——只返回特定数据库中特定表格的数据，不返回表格结构，添加“-t”命令参数

```mysql
mysqldump --u  b_user -h 101.3.20.33 -p'H_password' -t -P3306 database_di up_subjects  >0101_0630_up_subjects.sql
```

- 导出结构和数据:

```mysql
mysqldump -hlocalhost -uroot -P3306 -p6NbAFQBE  btmox>./btmox-data.sql
mysqldump -hlocalhost -uroot -P3306 -p6NbAFQBE  mxhy>./mxhy-data.sql
mysqldump -h132.72.192.432 -P3307 -uroot -p8888 htgl>d:\htgl.sql;
```

- 导入 `sql` 文件到数据库中：

```mysql
mysql -uroot  -P3306 -p6NbAFQBE  btmox< ./btmox-data.sql
mysql -uroot  -P3306 -p6NbAFQBE  mxhy< ./mxhy-data.sql
```

- 授权：

```mysql
grant all privileges on *.* to 'root'@'%' identified by '6NbAFQBE';
grant all privileges on *.* to 'dbmanager'@'%' identified by '6NbAFQBE';
FLUSH PRIVILEGES;
```

---



## 3、Mysql 的相关问题

### 3.1 事务的 4 大特性

`事务`：逻辑上的一组操作，要么全部执行，要么都不执行

|   atomic    | 原子性 | 事务的对小执行单位，不允许分割，要不全部成功，要不全部失败 |
| :---------: | :----: | ---------------------------------------------------------- |
| consistency | 一致性 | 执行事务前，数据保持一致，多个事务对同一数据的读取应该相同 |
|  isolation  | 独立性 | 并发访问数据库时，多个事务之间应该是独立的                 |
| determined  | 持久性 | 事务提交造成的数据影响是永久性的                           |

### 3.2 