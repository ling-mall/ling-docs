# MySQL

官方文档：<https://dev.mysql.com/doc/refman/8.2/en/>

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
```

创表语句

```sql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
(
    -----------------------------列定义-------------------------------
    -- 列名
    <col_name> 
    -- 数据类型
    <data_type>
    --------------------  
    -- 是否允许 NULL 值
    [NOT NULL | NULL]
    -- 默认值 可以是 literal(常量) 和 expr(表达式)
    [DEFAULT {literal | (expr)} ]
    -- 列的可见性，一个表必须至少有一列是可见的
    [VISIBLE | INVISIBLE]
    -- 自增，自增列的数据类型必须为 整数或浮点数类型，插入 null 值或 0 时将自增，每个表只能有一个自增列，且必须有索引，插入负值时会视为一个很大的正数； 
    -- MyISAM 引擎可以有多个自增列
    [AUTO_INCREMENT] 
    -- 唯一索引
    [UNIQUE [KEY]] 
    -- 主键索引
    [[PRIMARY] KEY]
    -- 注释
    [COMMENT 'string']
    -- 校对规则（排序规则）
    [COLLATE collation_name]
    ------------检查约束---------
    -- 添加检查约束 检查值是否符合规范 symbol 为检查约束的名称 expr 为表达式，可以使用列 例如 CHECK (列名1 > 列名2)，使用 NOT ENFORCED 则不会开启（但存在），需要通过 ALTER TABLE 表名 WITH CHECK CHECK CONSTRAINT 检查约束名称; 来开启
    [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]
    -- 更新时更新，比如 ON UPDATE CURRENT_TIMESTAMP ，会在更新当前行任意列更新时将本列更新为当前时间，不过插入时本列不能有值
    [ON UPDATE SET value]
)
--为自动递增列指定起始值和步长
AUTO_INCREMENT [=] value
-- 指定表的默认排序规则。
[DEFAULT] CHARACTER SET [=] charset_name
-- 指定表的默认字符集，用于存储字符数据。
[DEFAULT] COLLATE [=] collation_name
-- 为表添加注释或描述性文字
COMMENT [=] 'string'
-- 指定表的存储引擎。
ENGINE [=] engine_name
```

修改表语句

```sql
# 删除表
DROP TABLE <tableName>;
# 清空表
TRUNCATE TABLE <tableName>;
# 复制表结构
CREATE TABLE <new_table_name> LIKE <old_table_name>;
# 复制表结构与数据
CREATE TABLE <new_table_name> SELECT * FROM <old_table_name>
# 修改表名
RENAME TABLE <old_table_name> to <new_table_name>;
# 新增字段
ALTER TABLE <tableName> ADD <col_name> <data_type> [DEFAULT <default_value>] [NULL | NOT NULL] [FIRST | AFTER <col_name>]
# 修改字段
ALTER TABLE <tableName> MODIFY <col_name> <data_type> [DEFAULT <default_value>] [NULL | NOT NULL]
# 修改字段名
ALTER TABLE <tableName> CHANGE <old_col_name> <new_col>
# 删除字段
ALTER TABLE <tableName> DROP <col_name>
```

## 数据类型

| 数据类型           | 描述                                                                          | 存储范围                                             | 无符号存储范围               |
|--------------------|-------------------------------------------------------------------------------|-:-:--------------------------------------------------|------------------------------|
| TINYINT            | 存储小整数                                                                    | -128 到 127                                          | 0 到 255                     |
| SMALLINT           | 存储小整数                                                                    | -32768 到 32767                                      | 0 到 65535                   |
| MEDIUMINT          | 存储中等大小整数                                                              | -8388608 到 8388607                                  | 0 到 16777215                |
| INT                | 存储整数                                                                      | -2147483648 到 2147483647                            | 0 到 4294967295              |
| BIGINT             | 存储大整数                                                                    | -9223372036854775808 到 9223372036854775807          | 0 到 18446744073709551615    |
| FLOAT              | 存储单精度浮点数值                                                            | -3.402823466E+38 到 -1.175494351E-38,                | 0 到 3.402823466E+38         |
| DOUBLE             | 存储双精度浮点数值                                                            | -1.7976931348623157E+308 到 -2.2250738585072014E-308 | 0 到 1.7976931348623157E+308 |
| DECIMAL            | 存储精确的小数值，常用于货币等需要精确计算的场合                              | 依赖于指定的精度                                     | 依赖于指定的精度             |
| CHAR(n)            | 存储固定长度的字符串，最大长度为 n                                            | 最大长度为 255 字节                                  |                              |
| VARCHAR(n)         | 存储可变长度的字符串，最大长度为 n                                            | 最大长度为 65535 字节                                |                              |
| BINARY(n)          | 存储固定长度的二进制字符串，最大长度为 n                                      | 最大长度为 255 字节                                  |                              |
| VARBINARY(n)       | 存储可变长度的二进制字符串，最大长度为 n                                      | 最大长度为 65535 字节                                |                              |
| TINYBLOB           | 存储最大长度为 255 字节的二进制数据                                           |                                                      |                              |
| BLOB               | 存储最大长度为 65535 字节的二进制数据                                         |                                                      |                              |
| MEDIUMBLOB         | 存储最大长度为 16777215 字节的二进制数据                                      |                                                      |                              |
| LONGBLOB           | 存储最大长度为 4294967295 字节的二进制数据                                    |                                                      |                              |
| TINYTEXT           | 存储最大长度为 255 字节的文本数据                                             |                                                      |                              |
| TEXT               | 存储最大长度为 65535 字节的文本数据                                           |                                                      |                              |
| MEDIUMTEXT         | 存储最大长度为 16777215 字节的文本数据                                        |                                                      |                              |
| LONGTEXT           | 存储最大长度为 4294967295 字节的文本数据                                      |                                                      |                              |
| ENUM               | 存储枚举值，最多可以有 65535 个成员                                           |                                                      |                              |
| SET                | 存储一个集合，最多可以有 64 个成员                                            |                                                      |                              |
| DATE               | 存储日期，格式为 'YYYY-MM-DD'                                                 |                                                      |                              |
| TIME               | 存储时间，格式为 'HH:MM:SS'                                                   |                                                      |                              |
| TIMESTAMP          | 存储日期和时间，范围从 '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC |                                                      |                              |
| DATETIME           | 存储日期和时间，格式为 'YYYY-MM-DD HH:MM:SS'                                  |                                                      |                              |
| YEAR               | 存储年份，范围从 1901 到 2155                                                 | 1901 到 2155                                         | 1901 到 2155                 |
| BOOLEAN            | 存储布尔值，通常使用 TINYINT(1) 实现                                          | 0 到 1                                               | 0 到 1                       |
| GEOMETRY           | 用于存储任何类型的几何数据                                                    |                                                      |                              |
| POINT              | 用于存储单个点的坐标                                                          |                                                      |                              |
| LINESTRING         | 用于存储线的几何数据                                                          |                                                      |                              |
| POLYGON            | 用于存储多边形的几何数据                                                      |                                                      |                              |
| MULTIPOINT         | 用于存储多个点的几何数据                                                      |                                                      |                              |
| MULTILINESTRING    | 用于存储多条线的几何数据                                                      |                                                      |                              |
| MULTIPOLYGON       | 用于存储多个多边形的几何数据                                                  |                                                      |                              |
| GEOMETRYCOLLECTION | 用于存储一组几何对象的集合                                                    |                                                      |                              |

## 存储引擎

| 存储引擎      | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| InnoDB        | 支持事务、外键等功能，适合大型应用和高并发环境               |
| MyISAM        | 不支持事务和外键，适合读密集型的应用，比如数据仓库和归档系统 |
| MEMORY        | 将表数据存储在内存中，速度快，但数据不持久                   |
| NDB (Cluster) | 用于 MySQL Cluster，支持分布式存储和高可用性                 |
| ARCHIVE       | 用于存储归档数据，具有高压缩比和快速插入                     |
| CSV           | 用于存储数据到逗号分隔值文件中                               |
| BLACKHOLE     | 不会存储数据，用于复制和日志记录                             |
| FEDERATED     | 允许访问远程表的存储引擎                                     |

## 字符集与排序规则

字符集用于指定字符的编码，排序规则用于指定字符的排序规则。

排序规则含义

- CS ：区分大小写
- CI：不区会大小写
- AS：区分重音
- AI：不区分重音
- bin：根据每个位模式进行排序比较，即区分大小也，也区分重音。

常用字符集：

- `ascii`：美国信息交换标准代码，使用的128个字符。占用一个字节。
- `big5`：大五码，使用的是繁体中文。最多占用两个字节。
- `gb18030`：包含gb2312和gbk，支持简体中文，繁体中文和少数民族文字。最多4个字节。
- `gb2312`：支持简体中文，最多2个字节。
- `gbk`：支持简体中文，繁体中文，最多2个字节。
- `latin1`：Latin1是ISO-8859-1的别名，除ASCII收录的字符外，还包括西欧语言、希腊语、泰语、阿拉伯语、希伯来语对应的文字符号。占用一个字节。
- `utf8(utf8mb3)`：是针对Unicode的一种可变长度字符编码。支持 拉丁文、希腊文、西里尔字母、亚美尼亚语、希伯来文、阿拉伯文、叙利亚文， 中日韩文字、东南亚文字、中东文字等。最多3字节。
- `utf8mb4`：除了包含utf8 以外，还支持 Emoji 表情。最多4字节。

查看所支持的字符集

```sql
SHOW CHARACTER SET;
```

查看所支持的排序规则

```sql
SHOW COLLATION;
```

查看当前字符集

```sql
SHOW VARIABLES LIKE 'character_set_%';
```

查看当前排序规则

```sql
SHOW VARIABLES LIKE 'collation%';
```

创建数据库时指定字符集和排序规则

```sql
CREATE DATABASE <db_name> CHARACTER SET = <charset_name> COLLATE = <collation_name>;
```

创建表时给表指定字符集和排序规则

```sql
CREATE TABLE <table_name> (
   ...
) ENGINE=<engine_name> DEFAULT CHARSET=<charset_name> COLLATE=<collation_name>;
```

创表时给列指定字符集与排序规则

```sql
CREATE TABLE <table_name> (
  ...
   <column_name> <data_type> CHARACTER SET <charset_name> COLLATE <collation_name>
);
```

修改表时给列指定字符集与排序规则

```sql
ALTER TABLE <table_name> MODIFY <column_name> <data_type> CHARACTER SET <charset_name> COLLATE <collation_name>;
```

## 函数

### 时间

<https://dev.mysql.com/doc/refman/8.2/en/date-and-time-functions.html>

| 名字                                                                                                 | 描述                                                                 |
|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| ADDDATE(date,INTERVAL expr unit), ADDDATE(date,days)                                                 | 将时间值（间隔）添加到日期值                                         |
| ADDTIME(expr1,expr2)                                                                                 | 添加时间                                                             |
| CONVERT_TZ(dt,from_tz,to_tz)                                                                         | 从一个时区转换到另一个时区                                           |
| CURDATE()                                                                                            | 返回当前日期                                                         |
| CURRENT_DATE(), CURRENT_DATE                                                                         | CURDATE（） 的同义词                                                 |
| CURRENT_TIME(), CURRENT_TIME                                                                         | CURTIME（） 的同义词                                                 |
| CURRENT_TIMESTAMP(), CURRENT_TIMESTAMP                                                               | NOW（） 的同义词                                                     |
| CURTIME()                                                                                            | 返回当前时间                                                         |
| DATE(expr)                                                                                           | 提取日期或日期时间表达式的日期部分                                   |
| DATE_ADD(date,INTERVAL expr unit)                                                                    | 将时间值（间隔）添加到日期值                                         |
| DATE_FORMAT(date,format)                                                                             | 指定日期格式                                                         |
| DATE_SUB(date,INTERVAL expr unit)                                                                    | 从日期中减去时间值（间隔）                                           |
| DATEDIFF(expr1,expr2)                                                                                | 两个日期相减，expr1 - expr2 返回间隔天数，如果后一个更大则返回负数   |
| DAY(date)                                                                                            | DAYOFMONTH（） 的同义词   ，一个月的第几天                           |
| DAYNAME(date)                                                                                        | 返回星期几的名称                                                     |
| DAYOFMONTH(date)                                                                                     | 返回月份中的哪一天 （0-31）                                          |
| DAYOFWEEK(date)                                                                                      | 返回参数的星期几的索引（1-7），星期天为索引 1                        |
| DAYOFYEAR(date)                                                                                      | 返回一年中的某一天 （1-366）                                         |
| EXTRACT(unit FROM date)                                                                              | 提取日期的一部分                                                     |
| FROM_DAYS(number)                                                                                    | 将日期数字转换为日期                                                 |
| FROM_UNIXTIME(number)                                                                                | 将 Unix 时间戳格式化为日期                                           |
| GET_FORMAT({DATE&vert;TIME&vert;DATETIME}, {'EUR'&vert;'USA'&vert;'JIS'&vert;'ISO'&vert;'INTERNAL'}) | 返回日期格式字符串                                                   |
| HOUR(time)                                                                                           | 提取小时                                                             |
| LAST_DAY(date)                                                                                       | 返回参数的月份的最后一天的日期                                       |
| LOCALTIME(), LOCALTIME                                                                               | NOW（） 的同义词，返回当前日期                                       |
| LOCALTIMESTAMP, LOCALTIMESTAMP()                                                                     | NOW（） 的同义词，返回当前日期                                       |
| MAKEDATE(year,dayofyear)                                                                             | 从年份和年份中的某一天创建日期                                       |
| MAKETIME(hour,minute,second)                                                                         | 从小时、分钟、秒创建时间                                             |
| MICROSECOND(expr)                                                                                    | 从参数返回微秒                                                       |
| MINUTE(time)                                                                                         | 从参数中返回分钟                                                     |
| MONTH(date)                                                                                          | 返回月份                                                             |
| MONTHNAME(date)                                                                                      | 返回月份的名称                                                       |
| NOW()                                                                                                | 返回当前日期和时间                                                   |
| PERIOD_ADD()                                                                                         | 将期间添加到年-月                                                    |
| PERIOD_DIFF()                                                                                        | 返回期间之间的月数                                                   |
| QUARTER(date)                                                                                        | 从日期参数返回季度                                                   |
| SEC_TO_TIME(seconds)                                                                                 | 将秒转换为“hh：mm：ss”格式                                         |
| SECOND(time)                                                                                         | 返回第二个 （0-59）                                                  |
| STR_TO_DATE(str,format)                                                                              | 将字符串转换为日期                                                   |
| SUBDATE(date,INTERVAL expr unit), SUBDATE(expr,days)                                                 | 使用三个参数调用时 DATE\_SUB（） 的同义词                            |
| SUBTIME(expr1,expr2)                                                                                 | 减去时间                                                             |
| SYSDATE()                                                                                            | 返回函数的执行时间                                                   |
| TIME(expr)                                                                                           | 提取传递的表达式的时间部分                                           |
| TIME_FORMAT()                                                                                        | 格式化为时间                                                         |
| TIME_TO_SEC()                                                                                        | 返回转换为秒的参数                                                   |
| TIMEDIFF(expr1,expr2)                                                                                | 减去时间                                                             |
| TIMESTAMP(expr), TIMESTAMP(expr1,expr2)                                                              | 使用单个参数时，此函数返回日期或日期时间 表达;有两个参数，参数的总和 |
| TIMESTAMPADD(unit,interval,datetime_expr)                                                            | 向日期时间表达式添加间隔                                             |
| TIMESTAMPDIFF(unit,datetime_expr1,datetime_expr2)                                                    | 使用单位返回两个日期时间表达式的差值 指定                            |
| TO_DAYS(date)                                                                                        | 返回转换为天的日期参数                                               |
| TO_SECONDS(expr)                                                                                     | 返回转换为秒的日期或日期时间参数，因为 0年级                         |
| UNIX_TIMESTAMP([date])                                                                               | 返回 Unix 时间戳                                                     |
| UTC_DATE()                                                                                           | 返回当前 UTC 日期                                                    |
| UTC_TIME()                                                                                           | 返回当前 UTC 时间                                                    |
| UTC_TIMESTAMP()                                                                                      | 返回当前 UTC 日期和时间                                              |
| WEEK(date)                                                                                           | 返回周数                                                             |
| WEEKDAY(date)                                                                                        | 返回工作日索引                                                       |
| WEEKOFYEAR(date)                                                                                     | 返回日期的日历周 （1-53）                                            |
| YEAR(date)                                                                                           | 返回年份                                                             |
| YEARWEEK(date)                                                                                       | 返回年份和星期                                                       |

日期格式：

| 格式       | 描述                                                                    |
|------------|-------------------------------------------------------------------------|
| `%a`       | 工作日名称缩写 (..`Sun``Sat`)                                           |
| `%b`       | 缩写月份名称 （..`Jan``Dec`)                                            |
| `%c`       | 月份，数字 （..`0``12`)                                                 |
| `%D`       | 带有英文后缀的月份中的某一天（、、、、...）`0th``1st``2nd``3rd`         |
| `%d`       | 月份的某一天，数字 （..`00``31`)                                        |
| `%e`       | 月份的某一天，数字 （..`0``31`)                                         |
| `%f`       | 微秒 （..`000000``999999`)                                              |
| `%H`       | 小时 （..`00``23`)                                                      |
| `%h`       | 小时 （..`01``12`)                                                      |
| `%I`       | 小时 （..`01``12`)                                                      |
| `%i`       | 分钟，数字 （..`00``59`)                                                |
| `%j`       | 一年中的某一天 （..`001``366`)                                          |
| `%k`       | 小时 （..`0``23`)                                                       |
| `%l`       | 小时 （..`1``12`)                                                       |
| `%M`       | 月份名称 （..`January``December`)                                       |
| `%m`       | 月份，数字 （..`00``12`)                                                |
| `%p`       | `AM`或`PM`                                                              |
| `%r`       | 时间，12 小时（_`hh：mm：ss，`_后跟 或`AM``PM`)                         |
| `%S`       | 秒 （..`00``59`)                                                        |
| `%s`       | 秒 （..`00``59`)                                                        |
| `%T`       | 时间，24 小时 （_`hh：mm：ss`_)                                         |
| `%U`       | 周 （..），其中星期日是 一周的第一天;`WEEK（）` 模式 0`00``53`          |
| `%u`       | 周 （..），其中星期一是 一周的第一天;`WEEK（）`模式 1`00``53`           |
| `%V`       | 周 （..），其中星期日是 一周的第一天;`WEEK（）` 模式 2;用于`01``53``%X` |
| `%v`       | 周 （..），其中星期一是 一周的第一天;`WEEK（）`模式 3;用于`01``53``%x`  |
| `%W`       | 工作日名称 （..`Sunday``Saturday`)                                      |
| `%w`       | 星期几 （=星期天..=星期六）`0``6`                                       |
| `%X`       | 星期日是一周第一天的一周的年份，数字、 四位数;用于`%V`                  |
| `%x`       | 一周的年份，其中星期一是一周的第一天，数字， 四位数;用于`%v`            |
| `%Y`       | 年份，数字，四位数字                                                    |
| `%y`       | 年份，数字（两位数）                                                    |
| `%%`       | 文字字符`%`                                                             |
| ``%_`x`_`` | x，对于未列出的任何“_`x`_” 以上                                       |

### 字符串

| 函数名                               | 描述                                            |
|--------------------------------------|-------------------------------------------------|
| `CONCAT(str1, str2, ...)`            | 连接两个或多个字符串                            |
| `LENGTH(str)`                        | 返回字符串的长度                                |
| `LOWER(str)`                         | 将字符串转换为小写                              |
| `UPPER(str)`                         | 将字符串转换为大写                              |
| `REPLACE(str, from_str, to_str)`     | 在字符串中替换所有出现的子字符串                |
| `SUBSTRING(str, pos, len)`           | 从字符串中提取子字符串                          |
| `TRIM(str)`                          | 从字符串的两端删除空格                          |
| `ASCII(str)`                         | 返回字符串的第一个字符的ASCII值                 |
| `CHAR_LENGTH(str)`                   | 返回字符串的字符数                              |
| `CHARACTER_LENGTH(str)`              | 与`CHAR_LENGTH()`相同                           |
| `CONCAT_WS(separator,str1,str2,...)` | 与`CONCAT`类似，但是可以添加分隔符              |
| `FIELD(str,str1,str2,str3,...)`      | 返回str在str1, str2, str3, ...列表中的位置      |
| `FIND_IN_SET(str,strlist)`           | 如果字符串在字符串列表中，则返回位置，否则返回0 |
| `FORMAT(X,D)`                        | 格式化部分，D指定小数点后的小数位数             |
| `INSERT(str,pos,len,newstr)`         | 从指定位置开始，删除len个字符，并插入新字符串   |
| `INSTR(str,substr)`                  | 返回子字符串的第一次出现的位置                  |
| `LCASE(str)`                         | 与`LOWER()`相同                                 |
| `LEFT(str,len)`                      | 返回字符串的最左边的len个字符                   |
| `LOCATE(substr,str)`                 | 返回子字符串的第一次出现的位置                  |
| `LTRIM(str)`                         | 删除字符串的左边的空格                          |
| `MID(str,pos,len)`                   | 从字符串中提取子字符串，与`SUBSTRING()`相同     |
| `POSITION(substr IN str)`            | 与`LOCATE()`相同                                |
| `REPEAT(str,count)`                  | 重复字符串指定的次数                            |
| `REVERSE(str)`                       | 反转字符串                                      |
| `RIGHT(str,len)`                     | 返回字符串的最右边的len个字符                   |
| `RTRIM(str)`                         | 删除字符串的右边的空格                          |
| `SPACE(N)`                           | 返回N个空格字符                                 |
| `STRCMP(expr1,expr2)`                | 比较两个字符串                                  |
| `SUBSTR(str,pos)`                    | 从字符串中提取子字符串，与`SUBSTRING()`相同     |
| `SUBSTRING_INDEX(str,delim,count)`   | 返回子字符串的索引                              |
| `UCASE(str)`                         | 与`UPPER()`相同                                 |

### 数字

| 函数名                        | 描述                                    |
|-------------------------------|-----------------------------------------|
| `ABS(X)`                      | 返回X的绝对值                           |
| `ACOS(X)`                     | 返回X的反余弦值                         |
| `ASIN(X)`                     | 返回X的反正弦值                         |
| `ATAN(X)`                     | 返回X的反正切值                         |
| `ATAN2(X, Y)`                 | 返回X/Y的反正切值                       |
| `CEIL(X)`                     | 返回大于或等于X的最小整数               |
| `CEILING(X)`                  | 与`CEIL()`相同                          |
| `CONV(N,from_base,to_base)`   | 将数字N从from_base进制转换为to_base进制 |
| `COS(X)`                      | 返回X的余弦值                           |
| `COT(X)`                      | 返回X的余切值                           |
| `DEGREES(X)`                  | 将X从弧度转换为度                       |
| `DIV(X, Y)`                   | 返回X除以Y的结果                        |
| `EXP(X)`                      | 返回e的X次方                            |
| `FLOOR(X)`                    | 返回小于或等于X的最大整数               |
| `GREATEST(value1,value2,...)` | 返回参数列表中的最大值                  |
| `LEAST(value1,value2,...)`    | 返回参数列表中的最小值                  |
| `LN(X)`                       | 返回X的自然对数                         |
| `LOG(X)`                      | 返回X的自然对数                         |
| `LOG10(X)`                    | 返回X的以10为底的对数                   |
| `LOG2(X)`                     | 返回X的以2为底的对数                    |
| `MOD(X, Y)`                   | 返回X除以Y的余数                        |
| `PI()`                        | 返回圆周率                              |
| `POW(X, Y)`                   | 返回X的Y次方                            |
| `POWER(X, Y)`                 | 与`POW()`相同                           |
| `RADIANS(X)`                  | 将X从度转换为弧度                       |
| `RAND()`                      | 返回0到1之间的随机数                    |
| `ROUND(X, D)`                 | 返回X四舍五入到D小数位的值              |
| `SIGN(X)`                     | 返回X的符号                             |
| `SIN(X)`                      | 返回X的正弦值                           |
| `SQRT(X)`                     | 返回X的平方根                           |
| `TAN(X)`                      | 返回X的正切值                           |
| `TRUNCATE(X, D)`              | 返回X截断到D小数位的值                  |

### 流程控制

| 名字                                                                                             | 描述                            |
|--------------------------------------------------------------------------------------------------|---------------------------------|
| CASE value WHEN compare_value THEN result [WHEN compare_value THEN result ...] [ELSE result] END | 类似代码里的switch              |
| IF(expr1,expr2,expr3)                                                                            | If/else 构造                    |
| IFNULL()                                                                                         | Null if/else 构造               |
| NULLIF()                                                                                         | 如果 expr1 = expr2，则返回 NULL |
