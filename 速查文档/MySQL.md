# MySQL

## 登录 MySQL

```shell
mysql -h <host> -u <username> -p<password>
mysql -D <databaseName> -h <host> -u <username> -p
mysql -h <host> -P <port> -u <user> -p [db_name]
mysql -h <host> -u <user> -p [db_name]
```

## 数据库

```sql
-- 查看所有数据库
show databases
-- 选择数据库
use <databaseName>

-- 创建数据库
create [if not exists] database <databaseName>
-- 创建数据库的同时指定字符集与校对规则
create [if not exists] database <databaseName> default character set <字符集> default collate <校对规则>

-- 修改字符集与校对规则
alter database <databaseName> default character set <字符集> default collate <校对规则>

-- 删除数据库
drop [if exists] database <databaseName>
```

## 表操作

```sql
-- 查看所有表
show tables
-- 查看表结构
desc <tableName>
-- 查看表的详细信息
show create table <tableName>\G

-- 创建表
create [if not exists] table <tableName>(
    <columnName> <dataType> [primary key] [auto_increment] [not null] [default <defaultValue>] [COMMENT '<annotation>']
);
```
