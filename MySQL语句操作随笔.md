# MySQL语句操作随笔

### limit和offset用法

mysql里分页一般用limit来实现

select* from article LIMIT 1,3

select * from article LIMIT 3 OFFSET 1

上面两种写法都表示取2,3,4三条条数据

当limit后面跟两个参数的时候，第一个数表示要跳过的数量，后一位表示要取的数量,例如

select* from article LIMIT 1,3 就是跳过1条数据,从第2条数据开始取，取3条数据，也就是取2,3,4三条数据

当 limit后面跟一个参数的时候，该参数表示要取的数据的数量

例如 select* from article LIMIT 3 表示直接取前三条数据，类似sqlserver里的top语法。

当 limit和offset组合使用的时候，limit后面只能有一个参数，表示要取的的数量,offset表示要跳过的数量 。

例如select * from article LIMIT 3 OFFSET 1 表示跳过1条数据,从第2条数据开始取，取3条数据，也就是取2,3,4三条数据



### IFNULL

**Mysql查询中可以使用IFNULL()函数，IFNULL函数中有两个参数，它的作用是测试如果ifnull第一个参数不为null，就会显示出查询的结果，如果第一个参数为null，则会返回第二个参数的字符串**

示例要求：查询出户mobile为53437569的用户信息，若判断birthday为null，则用0替代，实现的mysql写法如下

```sql
select uuid,mobile,IFNULL(birthday, "0") as "birthday" from user where mobile="53437569"
```

```sql
select * IFNULL(birthday, "0") from user where mobile="53437569"
```



### 添加一个字段

```sql
alter table name add id int(11)
```

