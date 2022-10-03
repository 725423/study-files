# MySQL高级部分

#### MySQL UUID与自动递增INT作为主键

**优点**

使用`UUID`作为[主键](http://www.yiibai.com/mysql/uuid.html)具有以下优点：

- UUID值在表，数据库甚至在服务器上都是唯一的，允许您从不同数据库合并行或跨服务器分发数据库。
- UUID值不会公开有关数据的信息，因此它们在URL中使用更安全。例如，如果`ID`为`10`的客户通过URL：  http://www.example.com/customers/10/ 访问他的帐户，那么很容易猜到有一个客户`11`,`12`等，这可能是攻击的目标。
- 可以在避免往返数据库服务器的任何地方生成`UUID`值。它也简化了应用程序中的逻辑。 例如，要将数据插入到父表和子表中，必须首先插入父表，获取生成的`id`，然后将数据插入到子表中。通过使用`UUID`，可以生成父表的主键值，并在事务中同时在父表和子表中插入行。

**缺点**

除了优势之外，UUID值也有一些缺点：

- 存储UUID值(16字节)比整数(4字节)或甚至大整数(8字节)占用更多的存储空间。
- 调试似乎更加困难，想像一下`WHERE id ='9d6212cf-72fc-11e7-bdf0-f0def1e6646c'`和`WHERE id = 10`那个舒服一点？
- 使用UUID值可能会导致性能问题，因为它们的大小和没有被排序。、



#### MySQL UUID解决方案

在MySQL中，可以以紧凑格式(`BINARY`)存储UUID值，并通过以下功能显示人机可读格式([VARCHAR](http://www.yiibai.com/mysql/varchar.html))：

- *UUID_TO_BIN*
- *BIN_TO_UUID*
- *IS_UUID*

> 请注意，`UUID_TO_BIN()`，`BIN_TO_UUID()`和`IS_UUID()`函数仅在*MySQL 8.0*或更高版本中可用。

`UUID_TO_BIN()`函数将UUID从人类可读格式(`VARCHAR`)转换成用于存储的紧凑格式(`BINARY`)格式，并且`BIN_TO_UUID()`函数将UUID从紧凑格式(`BINARY`)转换为人类可读格式(`VARCHAR`)。

如果参数是有效的字符串格式`UUID`，`IS_UUID()`函数将返回`1`。 如果参数不是有效的字符串格式`UUID`，则`IS_UUID`函数返回`0`，如果参数为`NULL`，则`IS_UUID()`函数返回`NULL`。

以下是MySQL中有效的字符串格式`UUID`：

```sql
aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee
aaaaaaaabbbbccccddddeeeeeeeeeeee
{aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee}
```



#### MySQL UUID示例

```sql
#uuid示例
#创建一个名为customers的新表
CREATE TABLE customers ( id BINARY ( 16 ) PRIMARY KEY, NAME VARCHAR ( 255 ) ) #将UUID值插入到id列中，可以使用UUID()和UUID_TO_BIN()函数
INSERT INTO customers ( id, NAME )
VALUES
	( uuid_to_bin ( UUID()), 'cm1' ),
	( uuid_to_bin ( UUID()), 'cm2' ),
	( uuid_to_bin ( UUID()), 'cm3' ) #从UUID列查询数据，可以使用BIN_TO_UUID()函数将二进制格式转换为可读取的格式
SELECT
	bin_to_uuid ( id ) id,
NAME 
FROM
	customers
```



#### MySQL表中删除重复行

- 使用DELETE JOIN语句删除重复的行

```sql
#创建表
CREATE TABLE contacts (
	id INT auto_increment PRIMARY KEY,
	first_name VARCHAR ( 50 ) DEFAULT NULL,
	last_name VARCHAR ( 50 ) DEFAULT NULL,
	email VARCHAR ( 255 ) NOT NULL 
) DESC contacts 

#插入数据
INSERT INTO contacts ( first_name, last_name, email )
VALUES
	( 'Carine ', 'Schmitt', 'carine.schmitt@yiibai.com' ),
	( 'Jean', 'King', 'jean.king@gmail.com' ),
	( 'Peter', 'Ferguson', 'peter.ferguson@google.com' ),
	( 'Janine ', 'Labrune', 'janine.labrune@qq.com' ),
	( 'Jonas ', 'Bergulfsen', 'jonas.bergulfsen@mac.com' ),
	( 'Janine ', 'Labrune', 'janine.labrune@qq.com' ),
	( 'Susan', 'Nelson', 'susan.nelson@qq.com' ),
	( 'Zbyszek ', 'Piestrzeniewicz', 'zbyszek.piestrzeniewicz@att.com' ),
	( 'Roland', 'Keitel', 'roland.keitel@yahoo.com' ),
	( 'Julie', 'Murphy', 'julie.murphy@yahoo.com' ),
	( 'Kwai', 'Lee', 'kwai.lee@google.com' ),
	( 'Jean', 'King', 'jean.king@qq.com' ),
	( 'Susan', 'Nelson', 'susan.nelson@qq.com' ),
	( 'Roland', 'Keitel', 'roland.keitel@yahoo.com' );#查询返回contacts表中的重复email值

SELECT
	email,
	count( email ) 
FROM
	contacts 
GROUP BY
	email 
HAVING
	count( email ) > 1 
	
#使用DELETE JOIN语句删除重复的行
#删除重复的行并保持最高的ID
	DELETE t1 
FROM
	contacts AS t1
	INNER JOIN contacts AS t2 
WHERE
	t1.id < t2.id 
	AND t1.email = t2.email SELECT
	* 
FROM
	contacts
```

- 使用直接表删除重复的行
  - 以下是使用直接表删除重复行的步骤：
    - [创建一个新表](http://www.yiibai.com/mysql/create-table.html)，其结构与要删除重复行的原始表相同。
    - 将原始表中的[不同行](http://www.yiibai.com/mysql/insert-statement.html)插入直接表。
    - [删除原始表](http://www.yiibai.com/mysql/drop-table.html)并将直接表重命名为[原始表](http://www.yiibai.com/mysql/rename-table.html)

```sql
#使用直接表删除重复行
#创建一个新表，其结构与要删除重复行的原始表相同
CREATE TABLE contacts_copy LIKE contacts

desc contacts_copy

#将原始表中的不同行插入直接表
INSERT INTO contacts_copy ( id, first_name, last_name, email ) SELECT
id,
first_name,
last_name,
email 
FROM
	contacts 
GROUP BY
	email
	

#删除原始表并将直接表重新命名为原始表
drop table contacts;

alter table contacts_copy rename to contacts
```



#### MySQL中找到重复值

```sql
#创建一个名为contacts2表，其中包含四个列：id，first_name，last_name和email
create table if not exists contacts2(
		id int primary key auto_increment,
		first_name varchar(50) not null,
		last_name varchar(50) not null,
		email varchar(255) not null
)

#插入值
INSERT INTO contacts2 (first_name,last_name,email) 
VALUES ('Carine ','Schmitt','carine.schmitt@qq.com'),
       ('Jean','King','jean.king@yiibai.com'),
       ('Peter','Ferguson','peter.ferguson@google.com'),
       ('Janine ','Labrune','janine.labrune@aol.com'),
       ('Jonas ','Bergulfsen','jonas.bergulfsen@mac.com'),
       ('Janine ','Labrune','janine.labrune@aol.com'),
       ('Susan','Nelson','susan.nelson@qq.com'),
       ('Zbyszek ','Piestrzeniewicz','zbyszek.piestrzeniewicz@qq.com'),
       ('Roland','Keitel','roland.keitel@yahoo.com'),
       ('Julie','Murphy','julie.murphy@yahoo.com'),
       ('Kwai','Lee','kwai.lee@google.com'),
       ('Jean','King','jean.king@qq.com'),
       ('Susan','Nelson','susan.nelson@qq.comt'),
       ('Roland','Keitel','roland.keitel@yahoo.com')
			 
select * from contacts2
			 

#在一列中找到重复的值
#语法
SELECT
	col,
	COUNT( col ) 
FROM
	table_name 
GROUP BY
	col 
HAVING
	COUNT( col ) > 1;


#如果表中出现多个值，则该值将被视为重复。在这个语句中，使用COUNT函数的GROUP BY子句来计算指定列(col)的值。HAVING子句中的条件仅包含值count大于1的行，这些行是重复的行。
#使用此查询在contacts2表中查找具有重复email的所有行
SELECT
	email,
	count( email ) 
FROM
	contacts2 
GROUP BY
	email 
HAVING
	count( email ) > 1


#在多个列中查找重复值,语法
SELECT 
    col1, COUNT(col1),
    col2, COUNT(col2),
    ...

FROM
    table_name
GROUP BY 
    col1, 
    col2, ...
HAVING 
       (COUNT(col1) > 1) AND 
       (COUNT(col2) > 1) AND 


#只有当列的组合重复时，行才被认为是重复的，所以在HAVING子句中使用了AND运算符。
#例如，要使用first_name，last_name和email列中的重复值在contacts2表中查找行
SELECT
	first_name,
	count( first_name ),
	last_name,
	count( last_name ),
	email,
	count( email ) 
FROM
	contacts2 
GROUP BY
	first_name,
	last_name,
	email 
HAVING
	count( first_name ) > 1 
	AND count( last_name ) > 1 
	AND count( email ) > 1
```



#### MySQL复制表数据

```sql
#将部分数据复制到新表，语法
create table if not exists new_table 
select col1,col2,col3
from old_table
where conditions;



#上面的声明只是复制表及其数据。它不会复制与表关联的其他数据库对象，如索引，主键约束，外键约束，触发器等
#要从表中复制数据以及表的所有依赖对象，请使用以下语句
create table if not exists new_table like old_table;
insert new_table select * from old_table;


#mysql拷贝表示例
#将数据从offices表复制到示例数据(yiibaidb)库中指定名为offices_bk的新表
use yiibaidb
create table if not exists offices_bk 
select * from offices;

select * from offices_bk


#如果我们想仅复制在美国(USA)办公室，可以将WHERE子句添加到SELECT语句中
create table if not exists offices_usa
select * from offices
where country = 'USA'

select * from offices_usa


#假设不仅要复制数据，还要复制与offices表相关联的所有数据库对象
create table offices_dup like offices;
insert offices_dup 
select * from offices;

select * from offices_dup;



#mysql复制表到另一个数据库
#语法格式
create table destination_db.new_table
like source_db.old_table;

insert destination_db.new_table
select * from source_db.old_table;

#将其结构从示例数据库(yiibaidb)中的offices表复制出来，在test中创建了offices表
create table test.offices like yiibaidb.offices;

insert test.offices
select * from yiibaidb.offices;

#验证
select * from test.offices
```



#### MySQL REGEX：基于正则表达式的搜索

```sql
#MySQL REGEXP运算符
#WHERE子句中REGEXP运算符的语法
SELECT 
    column_list
FROM
    table_name
WHERE
    string_column REGEXP pattern;
```

- 此语句执行`string_column`与模式`pattern`匹配。
- 如果`string_column`中的值与模式`pattern`匹配，则`WHERE`子句中的表达式将返回`1`，否则返回`0`。
- 如果`string_column`或`pattern`为`NULL`，则结果为`NULL`。
- 除了`REGEXP`运算符之外，可以使用`RLIKE`运算符，这是`REGEXP`运算符的同义词。

```sql
REGEXP`运算符的否定形式是`NOT REGEXP
```

```sql
#MySQL REGEXP示例
#找出名字以字母A，B或C开头的产品。可以使用SELECT语句中的正则表达式如下
use yiibaidb

SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP '^(A|B|C)' 
ORDER BY
	productname;
```

- 字符`^`表示从字符串的开头匹配。
- 字符`|`如果无法匹配，则搜索替代方法。

下表说明了正则表达式中一些常用的元字符和构造。

| 元字符                                                       | 行为                             |
| ------------------------------------------------------------ | -------------------------------- |
| `^`                                                          | 匹配搜索字符串开头处的位置       |
| `$`                                                          | 匹配搜索字符串末尾的位置         |
| `.`                                                          | 匹配任何单个字符                 |
| `[…]`                                                        | 匹配方括号内的任何字符           |
| `[^…]`                                                       | 匹配方括号内未指定的任何字符     |
| ![img](http://www.yiibai.com/uploads/images/201707/3007/414100743_79929.png) | 匹配`p1`或`p2`模式               |
| `*`                                                          | 匹配前面的字符零次或多次         |
| `+`                                                          | 匹配前一个字符一次或多次         |
| `{n}`                                                        | 匹配前几个字符的`n`个实例        |
| `{m,n}`                                                      | 从`m`到`n`个前一个字符的实例匹配 |

```sql
#查找名称以a开头的产品，您可以在名称开头使用“^”进行匹配，如下查询语句
SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP '^a'


#使REGEXP运算符以区分大小写的方式比较字符串，可以使用BINARY运算符将字符串转换为二进制字符串
#MySQL比较二进制字节逐字节而不是逐字符。 这允许字符串比较区分大小写
#只匹配开头为大写“C”的产品名称
SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP BINARY '^C'
#报错： - Character set 'utf8_general_ci' cannot be used in conjunction with 'binary' in call to regexp_like.（未解决）


#找到以f结尾的产品，您可以使用’f$‘来匹配字符串的末尾
SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP 'f$'


#查找其名称包含“ford”的产品
SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP 'ford'


#查找名称只包含10个字符的产品，可以使用’^‘和’$‘来匹配产品名称的开头和结尾，并重复{10}次任何字符.，
SELECT
	productname 
FROM
	products 
WHERE
	productname REGEXP '^.{10}$'
```





#### MySQL row_number模拟

- row_number函数简介
  - `row_number`是一个排序函数，返回一行的顺序号，从第一行的`1`开始。经常想使用`row_number`函数来生成特定的报告。 

```sql
#MySQL row_number - 为每行添加行号
#从employees表中获取5名员工，并从1开始为每行添加行号
use yiibaidb

set @row_number = 0;

SELECT
	( @row_number := @row_number + 1 ) AS num,
	firstName,
	lastName 
FROM
	employees 
	LIMIT 5;

desc employees
```

在上述查询语句中：

- 在第一个语句中，定义了一个名为`row_number`的变量，并将其值设置为`0`。 `row_number`是由`@`前缀指示的会话变量。
- 在第二个语句中，从`employees`表中查询选择了数据，并将`row_number`变量的值增加到每行的`1`。`LIMIT`子句用于约束一些返回的行，在这种情况下，它被设置为`5`

```sql
#MySQL row_number - 为每个组添加行号
#看看payments表
SELECT
    customerNumber, paymentDate, amount
FROM
    payments
ORDER BY customerNumber;


#假设每个客户要添加一个行号，并且每当客户号码更改时，行号都会被重置
#可使用两个会话变量，一个用于行号，另一个用于存储旧客户编号，以将其与当前的客户编号进行比较，如下查询语句
SELECT 
    @row_number:=CASE
        WHEN @customer_no = customerNumber THEN @row_number + 1
        ELSE 1
    END AS num,
    @customer_no:=customerNumber as CustomerNumber,
    paymentDate,
    amount
FROM
    payments
ORDER BY customerNumber;
```

在查询中使用了[CASE语句](http://www.yiibai.com/mysql/case-statement.html)。 如果客户号码保持不变，我们增加了`row_number`变量的值，否则将`row_number`变量重置为`1` 





#### MySQL随机选择记录

- MySQL没有任何内置语句从数据库表中选择随机记录。为了实现这一点，您可以使用`RAND`函数。以下查询从数据库表中选择一个随机记录

```sql
SELECT 
    *
FROM
    tbl
ORDER BY RAND()
LIMIT 1;
```

- `RAND()`函数为表中的每一行生成一个随机值。
- [ORDER BY](http://www.yiibai.com/mysql/order-by.html)子句通过`RAND()`函数生成的随机数对表中的所有行进行排序。
- [LIMIT](http://www.yiibai.com/mysql/limit.html)子句选择随机排序的结果集中的第一行。

```sql
#MySQL使用INNER JOIN子句选择随机记录
#查询根据主键列生成随机数
SELECT
	ROUND(
	RAND() * ( SELECT max( id ) FROM TABLE )) AS id;
			
#通过上述查询返回的结果集连接表，如下所示
SELECT
	t.* 
FROM
	TABLE AS t
	JOIN ( SELECT ROUND( RAND() * ( SELECT MAX( id ) FROM TABLE )) AS id ) AS x 
WHERE
	t.id >= x.id 
	LIMIT 1;
			


#查询从customers表返回一个随机客户
SELECT
	t.customerNumber,
	t.customerName 
FROM
	customers AS t
	JOIN ( SELECT ROUND( RAND() * ( SELECT MAX( customerNumber ) FROM customers )) AS customerNumber ) AS x 
WHERE
	t.customerNumber >= x.customerNumber 
	LIMIT 1;
```





#### MySQL选择第n个最高纪录

使用[MAX](http://www.yiibai.com/mysql/max-function.html)或[MIN](http://www.yiibai.com/mysql/min.html)函数可以轻松选择数据库表中最高或最低的记录。但是，选择第`n`个最高纪录有点棘手。 例如，从`products`表中获得第二高价格的产品。

要选择第`n`个最高记录，需要执行以下步骤：

- 首先，得到`n`个最高记录，并按升序排列。第`n`个最高记录是结果集中的最后一个记录。
- 然后，按顺序对结果集进行排序，并获得第一个结果集

```sql
#以升序获得第n个最高记录的查询
SELECT
	* 
FROM
	table_name 
ORDER BY
	column_name ASC 
	LIMIT n 
	
#获得第n个最高记录的查询
SELECT
	* 
FROM
	( SELECT * FROM table_name ORDER BY column_name DESC LIMIT n ) AS tb1 
ORDER BY
	column_name ASC 
	LIMIT 1;
	
#MySQL为我们提供了限制返回结果集中的行数的LIMIT子句。可以重写上述查询如下
#查询返回n-1行之后的第一行，以便获得第n个最高记录
SELECT
	* 
FROM
	table_name 
ORDER BY
	column_name DESC 
	LIMIT n - 1,
	1;
	
#在products表中获得第二个最昂贵的产品(n = 2)
SELECT
	productCode,
	productName,
	buyPrice 
FROM
	products 
ORDER BY
	buyPrice DESC 
	LIMIT 1,
	1;
	
SELECT
	buyPrice 
FROM
	products 
ORDER BY
	buyPrice DESC 
	
#第二种方法
#获得第n个最高记录的第二种技术是使用MySQL子查询
SELECT
	* 
FROM
	table_name AS a 
WHERE
	n - 1 = ( SELECT COUNT( primary_key_column ) FROM products b WHERE b.column_name > a.column_name ) 
	
##查询返回n-1行之后的第一行，以便获得第n个最高记录
SELECT
	productCode,
	productName,
	buyPrice 
FROM
	products a 
WHERE
	1 = ( SELECT COUNT( productCode ) FROM products b WHERE b.buyPrice > a.buyPrice );
            
##查询返回n-1行之后的第一行，以便获得第n个最高记录
SELECT
	productCode,
	productName,
	buyPrice 
FROM
	( SELECT productCode, productName, buyPrice FROM products ORDER BY buyPrice DESC LIMIT 2 ) AS a 
ORDER BY
	buyPrice ASC 
	LIMIT 1;
```





#### MySQL获取今天的日期

```sql
#在查询中使用CURDATE()函数，并且希望将其替换为today()函数以使查询更易读，则可以按如下所示创建名为today()的自己存储的函数
DELIMITER $$
CREATE FUNCTION today () RETURNS DATE DETERMINISTIC BEGIN
	RETURN CURDATE();
	
END $$DELIMITER;


#查询今天
select today()


#查询明天
select today() + interval 1 day tomorrow


#查询昨天
select today() - interval 1 day yesterday


#查询前n天
select today() - interval n day n天前


#查询后n天
select today() + interval n day n天后
```





#### 索引

- 索引访问

  - 索引访问是通过遍历索引来直接访问表中记录行的方式

- 索引优点

  - 通过创建唯一索引可以保证数据库表中每一行数据的唯一性。
  - 可以给所有的 MySQL 列类型设置索引。
  - 可以大大加快数据的查询速度，这是使用索引最主要的原因。
  - 在实现数据的参考完整性方面可以加速表与表之间的连接。
  - 在使用分组和排序子句进行数据查询时也可以显著减少查询中分组和排序的时间

- 索引缺点

  - 创建和维护索引组要耗费时间，并且随着数据量的增加所耗费的时间也会增加。
  - 索引需要占磁盘空间，除了数据表占数据空间以外，每一个索引还要占一定的物理空间。如果有大量的索引，索引文件可能比数据文件更快达到最大文件尺寸。
  - 当对表中的数据进行增加、删除和修改的时候，索引也要动态维护，这样就降低了数据的维护速度

- 索引可以提高查询速度，但是会影响插入记录的速度。因为，向有索引的表中插入记录时，数据库系统会按照索引进行排序，这样就降低了插入记录的速度，插入大量记录时的速度影响会更加明显。这种情况下，最好的办法是先删除表中的索引，然后插入数据，插入完成后，再创建索引

- 创建索引的三种基本方法

  - 使用create index语句

    - ```sql
      create <索引名> on <表名> (<列名> [<长度>] [ ASC | DESC])
      ```

    - `<索引名>`：指定索引名。一个表可以创建多个索引，但每个索引在该表中的名称是唯一的。

    - `<表名>`：指定要创建索引的表名。

    - `<列名>`：指定要创建索引的列名。通常可以考虑将查询语句中在 JOIN 子句和 WHERE 子句里经常出现的列作为索引列。

    - `<长度>`：可选项。指定使用列前的 length 个字符来创建索引。使用列的一部分创建索引有利于减小索引文件的大小，节省索引列所占的空间。在某些情况下，只能对列的前缀进行索引。索引列的长度有一个最大上限 255 个字节（MyISAM 和 InnoDB 表的最大上限为 1000 个字节），如果索引列的长度超过了这个上限，就只能用列的前缀进行索引。另外，BLOB 或 TEXT 类型的列也必须使用前缀索引。

    - `ASC|DESC`：可选项。`ASC`指定索引按照升序来排列，`DESC`指定索引按照降序来排列，默认为`ASC`。

  - 使用create table 语句

    - 索引也可以在创建表（CREATE TABLE）的同时创建。在 CREATE TABLE 语句中添加以下语句。语法格式：

      ```sql
      CONSTRAINT PRIMARY KEY [索引类型] (<列名>,…)
      ```

    - 在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的主键。

      ```sql
      KEY | INDEX [<索引名>] [<索引类型>] (<列名>,…)
      ```

    - 在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的索引

      ```sql
      UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)
      ```

    - 在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的唯一性索引

      ```sql
      FOREIGN KEY <索引名> <列名>
      ```

    - 在 CREATE TABLE 语句中添加此语句，表示在创建新表的同时创建该表的外键。

  - 注意：在使用 CREATE TABLE 语句定义列选项的时候，可以通过直接在某个列定义后面添加 PRIMARY KEY 的方式创建主键。而当主键是由多个列组成的多列索引时，则不能使用这种方法，只能用在语句的最后加上一个 PRIMARY KRY(<列名>，…) 子句的方式来实现。

  - 使用alter table语句

    - CREATE INDEX 语句可以在一个已有的表上创建索引，ALTER TABLE 语句也可以在一个已有的表上创建索引。在使用 ALTER TABLE 语句修改表的同时，可以向已有的表添加索引。具体的做法是在 ALTER TABLE 语句中添加以下语法成分的某一项或几项。

      ```sql
      ADD INDEX [<索引名>] [<索引类型>] (<列名>,…)
      ```

    - 在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加索引。

      ```sql
      ADD PRIMARY KEY [<索引类型>] (<列名>,…)
      ```

    - 在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加主键。

      ```sql
      ADD UNIQUE [ INDEX | KEY] [<索引名>] [<索引类型>] (<列名>,…)
      ```

    - 在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加唯一性索引。

      ```sql
      ADD FOREIGN KEY [<索引名>] (<列名>,…)
      ```

    - 在 ALTER TABLE 语句中添加此语法成分，表示在修改表的同时为该表添加外键。

  - 创建普通索引

    ```sql
    CREATE TABLE tb_stu_info
        -> (
        -> id INT NOT NULL,
        -> name CHAR(45) DEFAULT NULL,
        -> dept_id INT DEFAULT NULL,
        -> age INT DEFAULT NULL,
        -> height INT DEFAULT NULL,
        -> INDEX(height)
        -> );
    ```

    ```sql
    CREATE TABLE `tb_stu_info` (
      `id` int(11) NOT NULL,
      `name` char(45) DEFAULT NULL,
      `dept_id` int(11) DEFAULT NULL,
      `age` int(11) DEFAULT NULL,
      `height` int(11) DEFAULT NULL,
      KEY `height` (`height`)
    ) ENGINE=InnoDB DEFAULT CHARSET=gb2312
    ```

  - 创建唯一索引

    ```sql
    CREATE TABLE tb_stu_info2
        -> (
        -> id INT NOT NULL,
        -> name CHAR(45) DEFAULT NULL,
        -> dept_id INT DEFAULT NULL,
        -> age INT DEFAULT NULL,
        -> height INT DEFAULT NULL,
        -> UNIQUE INDEX(height)
        -> );
    ```

    ```sql
    CREATE TABLE `tb_stu_info2` (
      `id` int(11) NOT NULL,
      `name` char(45) DEFAULT NULL,
      `dept_id` int(11) DEFAULT NULL,
      `age` int(11) DEFAULT NULL,
      `height` int(11) DEFAULT NULL,
      UNIQUE KEY `height` (`height`)
    ) ENGINE=InnoDB DEFAULT CHARSET=gb2312
    ```

  - 查看索引

    ```sql
    show index from <表名> [from <数据库名>]
    ```

    | 参数         | 说明                                                         |
    | ------------ | ------------------------------------------------------------ |
    | Table        | 表示创建索引的数据表名，这里是 tb_stu_info2 数据表。         |
    | Non_unique   | 表示该索引是否是唯一索引。若不是唯一索引，则该列的值为 1；若是唯一索引，则该列的值为 0。 |
    | Key_name     | 表示索引的名称。                                             |
    | Seq_in_index | 表示该列在索引中的位置，如果索引是单列的，则该列的值为 1；如果索引是组合索引，则该列的值为每列在索引定义中的顺序。 |
    | Column_name  | 表示定义索引的列字段。                                       |
    | Collation    | 表示列以何种顺序存储在索引中。在 MySQL 中，升序显示值“A”（升序），若显示为 NULL，则表示无分类。 |
    | Cardinality  | 索引中唯一值数目的估计值。基数根据被存储为整数的统计数据计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL 使用该索引的机会就越大。 |
    | Sub_part     | 表示列中被编入索引的字符的数量。若列只是部分被编入索引，则该列的值为被编入索引的字符的数目；若整列被编入索引，则该列的值为 NULL。 |
    | Packed       | 指示关键字如何被压缩。若没有被压缩，值为 NULL。              |
    | Null         | 用于显示索引列中是否包含 NULL。若列含有 NULL，该列的值为 YES。若没有，则该列的值为 NO。 |
    | Index_type   | 显示索引使用的类型和方法（BTREE、FULLTEXT、HASH、RTREE）。   |
    | Comment      | 显示评注。                                                   |

  - 修改和删除索引

    - 删除索引是指将表中已经存在的索引删除掉。不用的索引建议进行删除，因为它们会降低表的更新速度，影响数据库的性能。对于这样的索引，应该将其删除。

    - 在 [MySQL](http://c.biancheng.net/mysql/) 中修改索引可以通过删除原索引，再根据需要创建一个同名的索引，从而实现修改索引的操作

    - 使用drop index语句

      ```sql
      DROP INDEX <索引名> ON <表名>
      ```

    - 使用alter table语句

      - 根据 ALTER TABLE 语句的语法可知，该语句也可以用于删除索引。具体使用方法是将 ALTER TABLE 语句的语法中部分指定为以下子句中的某一项。

        - DROP PRIMARY KEY：表示删除表中的主键。一个表只有一个主键，主键也是一个索引。
        - DROP INDEX index_name：表示删除名称为 index_name 的索引。
        - DROP FOREIGN KEY fk_symbol：表示删除外键。

        > 注意：如果删除的列是索引的组成部分，那么在删除该列时，也会将该列从索引中删除；如果组成索引的所有列都被删除，那么整个索引将被删除。
