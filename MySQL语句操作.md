# MySQL语句操作

### select语句语法

- ![image-20220918093718094](C:\Users\22906\AppData\Roaming\Typora\typora-user-images\image-20220918093718094.png)

`SELECT`语句由以下列表中所述的几个子句组成：

- `SELECT`之后是逗号分隔列或星号(`*`)的列表，表示要返回所有列。
- `FROM`指定要查询数据的表或视图。
- `JOIN`根据某些连接条件从其他表中获取数据。
- `WHERE`过滤结果集中的行。
- `GROUP BY`将一组行组合成小分组，并对每个小分组应用聚合函数。
- `HAVING`过滤器基于`GROUP BY`子句定义的小分组。
- `ORDER BY`指定用于排序的列的列表。
- `LIMIT`限制返回行的数量。



使用星号(`*`)进行测试。建议显式获取数据的列，原因如下：

- 使用星号(`*`)可能会返回不使用的列的数据。 它在MySQL数据库服务器和应用程序之间**产生不必要的*I/O*磁盘和网络流量**。
- 如果明确指定列，则结果集更可预测并且更易于管理。 想象一下，当您使用星号(`*`)并且有人通过添加更多列来更改表格数据时，将会得到一个与预期不同的结果集。
- 使用星号(`*`)**可能会将敏感信息暴露给未经授权的用户**。



### MySQL插入数据

```sql
select * from tasks

desc tasks  #查询表结构


#插入多行数据
insert into tasks(subject, start_date, end_date, description)
values ('任务一','2022-03-04','2022-03-25','Description 1'),
				('任务二','2022-04-04','2022-04-25','Description 2'),
				('任务三','2022-05-04','2022-05-25','Description 3')
				
				
#具有SELECT子句的MySQL INSERT
#使用INSERT和SELECT子句完全或部分复制表
insert into table_1
select c1, c2 from table_2

#案例
#假设将tasks表复制一份

#首先，通过复制tasks表的结构，创建一个名为tasks_bak的新表
create table tasks_bak like tasks

#第二步，使用以下INSERT语句将tasks表中的数据插入tasks_bak表
insert into tasks_bak
select * from tasks

#检查一下
select * from tasks_bak
```



### 更新表数据

```sql
#MySQL  update语法

UPDATE [LOW_PRIORITY] [IGNORE] table_name 
SET 
    column_name1 = expr1,
    column_name2 = expr2,
    ...
WHERE
    condition;


#MySQL在UPDATE语句中支持两个修饰符。

#LOW_PRIORITY修饰符指示UPDATE语句延迟更新，直到没有从表中读取数据的连接。 LOW_PRIORITY对仅使用表级锁定的存储引擎(例如MyISAM，MERGE，MEMORY)生效。

#即使发生错误，IGNORE修饰符也可以使UPDATE语句继续更新行。导致错误(如重复键冲突)的行不会更新。

#查询Mary的电子邮件信息
select firstname, lastname, email
from employees
where employeeNumber = 1056


#更新电子邮件
update employees
set email = 'new cm@zb.com'
where employeeNumber = 1056


#更新多列,更新员工编号1056的姓氏和电子邮件列
		update employees
set 
		lastname = 'cm',
		email = 'new2 cm@zb.com'
where 
		employeeNumber = 1056;
		
		
#查询customer表中没有销售代表的顾客
select customerName, salesRepEmployeeNumber
from customers
where salesRepEmployeeNumber is null
		

#从employees表中随机选择一个其职位是Sales Rep的员工
select employeeNumber
from employees
WHERE jobTitle = 'Sales Rep'
order by RAND()
limit 1


#将选中的员工更新到customer表中，更新customers表中的销售代表员工编号(salesRepEmployeeNumber)列
update customers
set
		salesRepEmployeeNumber = (select
		employeeNumber
		from employees
		where jobTitle = 'Sales Rep'
		limit 1)
where salesRepEmployeeNumber is null


#再次查询customer表中salesRepEmployeeNumber值
select customerName, salesRepEmployeeNumber
from customers
```



### MySQL删除表数据

```sql
#delete语法
delete from table_name
where condition

#删除officeNumber为4的员工
delete from employees
where officeCode = 4


#限制要删除的行数，则使用LIMIT子句
delete from table
limit row_count

#请注意，表中的行顺序未指定，因此，当您使用LIMIT子句时，应始终使用ORDER BY子句，不然删除的记录可能不是你所预期的那样
delete from table_name
order by c1, c1,...
limit row_count


desc customers

select customerName from customers

#以下语句按客户名称按字母排序客户，并删除前10个客户
delete from customers
order by customerName
limit 10


#以下DELETE语句选择法国(France)的客户，按升序按信用额度(creditLimit)进行排序，并删除前5个客户
DELETE FROM customers
WHERE country = 'France'
ORDER BY creditLimit
LIMIT 5

select customerName,country from customers where country = 'France'
```



### 创建与删除数据库

```sql
#数据库创建语句
create database [if not exists] database_name


#显示数据库
show databases

#选择使用的数据库
use database_name

#删除数据库
drop database [if exists] database_name
```



### 创建表

```sql
#创建表create table 语句的语法
CREATE TABLE [IF NOT EXISTS] table_name(
        column_list
) engine=table_type;

#要在CREATE TABLE语句中为表定义列，请使用以下语法
column_name data_type[size] [NOT NULL|NULL] [DEFAULT value] 
[AUTO_INCREMENT]

#如果要将表的特定列设置为主键，则使用以下语法
primary key (col1, col2,...)

#例子使用CREATE TABLE语句创建这个tasks表
CREATE TABLE IF NOT EXISTS tasks (
  task_id INT(11) NOT NULL AUTO_INCREMENT,
  subject VARCHAR(45) DEFAULT NULL,
  start_date DATE DEFAULT NULL,
  end_date DATE DEFAULT NULL,
  description VARCHAR(200) DEFAULT NULL,
  PRIMARY KEY (task_id)
) ENGINE=InnoDB;
```

以上定义列的语法中最重要的组成部分是：

- `column_name`指定列的名称。每列具有特定数据类型和大小，例如：`VARCHAR(255)`。
- `NOT NULL`或`NULL`表示该列是否接受`NULL`值。
- `DEFAULT`值用于指定列的默认值。
- `AUTO_INCREMENT`指示每当将新行插入到表中时，列的值会自动增加。每个表都有一个且只有一个`AUTO_INCREMENT`列。



### 修改表结构

- `ALTER TABLE`语句来更改现有表结构(如添加或删除列，更改列属性等）
- `ALTER TABLE`语句可用来[添加列](http://www.yiibai.com/mysql/add-column.html)，[删除列](http://www.yiibai.com/mysql/drop-column.html)，更改列的[数据类型](http://www.yiibai.com/mysql/data-types.html)，[添加主键](http://www.yiibai.com/mysql/primary-key.html)，[重命名表](http://www.yiibai.com/mysql/rename-table.html)等等。 以下说明了`ALTER TABLE`语句语法

```sql
#创建表create table 语句的语法
CREATE TABLE [IF NOT EXISTS] table_name(
        column_list
) engine=table_type;

#要在CREATE TABLE语句中为表定义列，请使用以下语法
column_name data_type[size] [NOT NULL|NULL] [DEFAULT value] 
[AUTO_INCREMENT]

#如果要将表的特定列设置为主键，则使用以下语法
primary key (col1, col2,...)

#例子使用CREATE TABLE语句创建这个tasks表
CREATE TABLE IF NOT EXISTS tasks (
  task_id INT(11) NOT NULL AUTO_INCREMENT,
  subject VARCHAR(45) DEFAULT NULL,
  start_date DATE DEFAULT NULL,
  end_date DATE DEFAULT NULL,
  description VARCHAR(200) DEFAULT NULL,
  PRIMARY KEY (task_id)
) ENGINE=InnoDB;


#alter table语句语法
alter table table_name action1[,action2,...]


#创建新表
drop table if exists tasks;
create table tasks(
		task_id int not null,
		subject varchar(45) null,
		start_date date null,
		end_date date null,
		description varchar(200) null,
		primary key (task_id),
		unique index task_id_unique (task_id asc)
);


#假设您希望在任务表中插入新行时，task_id列的值会自动增加1。那么可以使用ALTER TABLE语句将task_id列的属性设置为AUTO_INCREMENT
alter table tasks 
change column task_id task_id int(11) not null auto_increment;

#在tasks表中插入一些行数据来验证更改
insert into tasks (subject, start_date, end_date, description)
values ('nihao', NOW(), NOW(),'welcome')


insert into tasks (subject, start_date, end_date, description)
values ('hello', NOW(), NOW(),'welcome')

select * from tasks

#添加一个名为complete的新列以便在任务表中存储每个任务的完成百分比。
alter table tasks 
add column complete decimal(2,1) null
after description


#删除tasks表的description列
alter table tasks
drop column description


#重命名表
alter table tasks
remove to tasks_02
```

### 重命名表

```sql
#更改一个或多个表
rename table old_table_name to new_table_name
```

除了表之外，我们还可以使用`RENAME TABLE`语句来重命名[视图](http://www.yiibai.com/mysql/views.html)。

在执行`RENAME TABLE`语句之前，必须确保没有活动事务或[锁定表](http://www.yiibai.com/mysql/table-locking.html)。

> 请注意，不能使用`RENAME TABLE`语句来重命名[临时表](http://www.yiibai.com/mysql/temporary-table.html)，但可以使用[ALTER TABLE语句](http://www.yiibai.com/mysql/alter-table.html)重命名临时表

- 重命名视图引用的表

```sql
#创建视图
create view v_employee_info as
		select 
				id,first_name,last_name,dept_name
		from 
				employees
						inner join
				departments using (department_id)
#using(id)相当于on a.id = b.id

#查看视图
select * from v_employee_info


#将v_employee_info视图中的employees表重命名为people，并查询视图的数据
rename table employees to people

#使用CHECK TABLE语句来检查v_employee_info视图的状态如下
check table v_employee_info

#需要手动更改v_employee_info视图，以便它引用people表而不是employees表
```

- 重命名由存储过程引用的表

```sql
#如果要重命名由存储过程引用的表，则必须像对视图一样进行手动调整
#创建一个名为get_employee的新存储过程，该过程引用employees表
delimiter $$

create procedure get_employee(in p_id int)

begin 
	select first_name,
					last_name,
					dept_name
	from employees
	inner join departments using(department_id)
	where id = p_id;
end $$

delimiter;


#再次将employees表重新命名为people表
rename table employees to people

#执行get_employee存储过程从employees表来获取id为1的员工的数据
call get_employee(2)

#要解决这个问题，我们必须手动将存储过程中的employees表更改为people表
```



### MySQL的数据类型

- 它用来表示数据值。
- 占用的空间以及值是固定长度还是可变长度。
- 数据类型的值可以被索引。
- MySQL如何比较特定数据类型的值。

| 数据类型     | 指定值和范围                                      |
| ------------ | ------------------------------------------------- |
| `char`       | String(0~255)                                     |
| `varchar`    | String(0~255)                                     |
| `tinytext`   | String(0~255)                                     |
| `text`       | String(0~65536)                                   |
| `blob`       | String(0~65536)                                   |
| `mediumtext` | String(0~16777215)                                |
| `mediumblob` | String(0~16777215)                                |
| `longblob`   | String(0~4294967295)                              |
| `longtext`   | String(0~4294967295)                              |
| `tinyint`    | Integer(-128~127)                                 |
| `smallint`   | Integer(-32768~32767)                             |
| `mediumint`  | Integer(-8388608~8388607)                         |
| `int`        | Integer(-214847668~214847667)                     |
| `bigint`     | Integer(-9223372036854775808~9223372036854775807) |
| `float`      | decimal(精确到23位小数)                           |
| `double`     | decimal(24~54位小数)                              |
| `decimal`    | 将`double`转储为字符串形式                        |
| `date`       | *YYYY-MM-DD*                                      |
| `datetime`   | *YYYY-MM-DD HH:MM:SS*                             |
| `timestamp`  | *YYYYMMDDHHMMSS*                                  |
| `time`       | *HH:MM:SS*                                        |
| `enum`       | 选项值之一                                        |
| `set`        | 选项值子集                                        |
| `boolean`    | tinyint(1)                                        |



#### MySQL数值数据类型

在MySQL中，您可以找到所有SQL标准数字类型，包括精确数字数据类型和近似数字数据类型，包括整数，定点和浮点数。 此外，MySQL还具有用于存储位值的[BIT](http://www.yiibai.com/mysql/bit.html)数据类型。数字类型可以是有符号或无符号，但`BIT`类型除外。

下表显示了MySQL中数字类型的总结：

| 数字类型                                            | 描述               |
| --------------------------------------------------- | ------------------ |
| [TINYINT](http://www.yiibai.com/mysql/int.html)     | 一个很小的整数     |
| [SMALLINT](http://www.yiibai.com/mysql/int.html)    | 一个小的整数       |
| [MEDIUMINT](http://www.yiibai.com/mysql/int.html)   | 一个中等大小的整数 |
| [INT](http://www.yiibai.com/mysql/int.html)         | 一个标准整数       |
| [BIGINT](http://www.yiibai.com/mysql/int.html)      | 一个大整数         |
| [DECIMAL](http://www.yiibai.com/mysql/decimal.html) | 定点数             |
| `FLOAT`                                             | 单精度浮点数       |
| `DOUBLE`                                            | 双精度浮点数       |
| [BIT](http://www.yiibai.com/mysql/bit.html)         | 一个字节字段       |



#### MySQL布尔数据类型

MySQL没有内置的 [BOOLEAN](http://www.yiibai.com/mysql/boolean.html) 或 [BOOL](http://www.yiibai.com/mysql/boolean.html) 数据类型。所以要表示布尔值，MySQL使用最小的整数类型，也就是`TINYINT(1)`。 换句话说，`BOOLEAN`和`BOOL`是`TINYINT(1)`的同义词。



#### MySQL字符串数据类型

在MySQL中，字符串可以容纳从纯文本到二进制数据(如图像或文件)的任何内容。可以通过使用[LIKE](http://www.yiibai.com/mysql/like.html)运算符，[正则表达](http://www.yiibai.com/mysql/regular-expression-regexp.html)式和[全文搜索](http://www.yiibai.com/mysql/regular-expression-regexp.html)，根据模式匹配来比较和搜索字符串。

下表显示了MySQL中的字符串数据类型：

| 字符串类型                                              | 描述                                      |
| ------------------------------------------------------- | ----------------------------------------- |
| [char](http://www.yiibai.com/mysql/char-data-type.html) | 固定长度的非二进制(字符)字符串            |
| [varchar](http://www.yiibai.com/mysql/varchar.html)     | 可变长度的非二进制字符串                  |
| `BINARY`                                                | 一个固定长度的二进制字符串                |
| `VARBINARY`                                             | 一个可变长度的二进制字符串                |
| `TINYBLOB`                                              | 一个非常小的BLOB(二进制大对象)            |
| `BLOB`                                                  | 一个小的BLOB(二进制大对象)                |
| `MEDIUMBLOB`                                            | 一个中等大小的BLOB(二进制大对象)          |
| `LONGBLOB`                                              | 一个大的BLOB(二进制大对象)                |
| [TINYTEXT](http://www.yiibai.com/mysql/text.html)       | 一个非常小的非二进制字符串                |
| [TEXT](http://www.yiibai.com/mysql/text.html)           | 一个小的非二进制字符串                    |
| [MEDIUMTEXT](http://www.yiibai.com/mysql/text.html)     | 一个中等大小的非二进制字符串              |
| [LONGTEXT](http://www.yiibai.com/mysql/text.html)       | 一个很大的非二进制字符串                  |
| [ENUM](http://www.yiibai.com/mysql/enum.html)           | 枚举; 每个列值可以被分配一个枚举成员      |
| `SET`                                                   | 集合; 每个列值可以分配零个或多个`SET`成员 |



#### MySQL日期和时间数据类型

MySQL提供日期和时间的类型以及日期和时间的组合。 此外，MySQL还支持[时间戳](http://www.yiibai.com/mysql/timestamp.html)数据类型，用于跟踪表的一行中的更改。如果只想存储没有日期和月份的年份数据，则可以使用`YEAR`数据类型。

下表说明了MySQL日期和时间数据类型：

| 字符串类型                                              | 描述                                    |
| ------------------------------------------------------- | --------------------------------------- |
| [DATE](http://www.yiibai.com/mysql/date.html)           | `YYYY-MM-DD`格式的日期值                |
| [TIME](http://www.yiibai.com/mysql/time.html)           | `hh:mm:ss`格式的时间值                  |
| [DATETIME](http://www.yiibai.com/mysql/datetime.html)   | `YYYY-MM-DD hh:mm:ss`格式的日期和时间值 |
| [TIMESTAMP](http://www.yiibai.com/mysql/timestamp.html) | `YYYY-MM-DD hh:mm:ss`格式的时间戳记值   |
| `YEAR`                                                  | `YYYY`或`YY`格式的年值                  |





#### MySQL空间数据类型

MySQL支持许多包含各种几何和地理值的空间数据类型，如下表所示：

| 字符串类型         | 描述                      |
| ------------------ | ------------------------- |
| GEOMETRY           | 任何类型的空间值          |
| POINT              | 一个点(一对X-Y坐标)       |
| LINESTRING         | 曲线(一个或多个`POINT`值) |
| POLYGON            | 多边形                    |
| GEOMETRYCOLLECTION | `GEOMETRY`值的集合        |
| MULTILINESTRING    | `LINESTRING`值的集合      |
| MULTIPOINT         | `POINT`值的集合           |
| MULTIPOLYGON       | `POLYGON`值的集合         |
