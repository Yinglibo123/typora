# 了解SQL

## 数据库基础

### 数据库&表

**数据库：**保存有组织的数据的容器（通常是一个文件或一组文件）。

**数据库管理系统（DBMS）：**数据库软件

数据库是通过DBMS创建和操纵的容器

**表（table）：**某种特定类型数据的结构化清单。(存储在表中的数据是同一种类型的数据或清单)

**表名：**一个数据库中表名是唯一的，不同的数据库中表名可以相同。

**模式：**数据库和表的布局及特性的信息

**列：**表中的一个字段，所有的表都是由一个或者多个列组成的

**数据类型：**每个表列都有相应的数据类型

**行：**表中的一个记录

### 主键

**主键：**一列（或者几列）。主键的值可以唯一的标识表中的每一行。（主键必须包含唯一值。 如果主键由多个列组成，则这些列中的值组合必须是唯一的，但是单个列的值可以不唯一）

*提示：保证创建的每个表具有一个主键，以便于以后的数据操作和管理*

作为主键的列需要满足：

- 任何两行的主键值都不相同
- 每一行都有主键值（不能为NULL）
- 主键值不允许修改
- 主键值不能重用（如果某行从表中删除，它的主键不能赋给以后的新行）



## SQL

**SQL：**structured query language，结构化查询语言，用来与数据库沟通的语言。

**SQL的优点：**一种通用的语言，几乎所有的DBMS都支持SQL；简单；强有力



# 数据索引

**SELECT，FROM：**SELECT之后接列名，FROM指示从哪个表中检索数据

多条SQL语句之间必须用";"进行分隔开

SQL语句不区分大小写

可以同时检索多个列：select prod_id,prod_name,prod_price from products;

**通配符***：可以检索出所有的列, select * from products;    甚至可以检索出名字未知的列

**DISTINCT：**返回不同的值,select distinct vend_id from products;

*DISTINCT关键字不可以部分使用，它作用于所有的列，除非指定的两列完全相同，否则所有的行都会被检索出来*

**LIMIT:** 在mysql中返回指定数量的行，select vend_id from products limit 2;

**OFFSET:**	表示从哪里开始，select vend_id from products limit 4 offset 1;返回从第一行开始的4行，起始为第0行。

Offset可以省略,select vend_id from products limit 1,4;第一个数字：偏移，第二个：返回的行数。

在不同的DBMS中，指定返回行数的关键字不一样

**注释：**单行注释#，或者--，多行注释/*     */

# 排序索引数据

**关键字：**ORDER BY

要求ORDER BY放在SELECT语句的最后；

可以使用非检索的列排序数据。select vend_id from products order by prod_id;

**对多个列进行排序：**select * from products order by vend_id,prod_price;(首先对vend_id排序，然后对prod_price排序，注意只有当vend_id相同的时候，才会使用prod_price进行排序。如果vend_id各不相同，那么不会使用prod_id进行排序)

**使用相对位置排序：**select vend_id,prod_id from products order by 1,2;   其中1表示SELCT中的第一列vend_id,2表示prod_id，当根据不出现在 SELECT 清单中的列进行排序时，不能采用这项技术。

**指定排序的方向：**默认采用升序ASC排列（A到Z），采用关键字DESC可以实现降序。select vend_id from products order by vend_id DESC；	

*DESC只会作用到在它前面的列，如果想要在多列上采用降序，需要为相应的列后面都加上DESC*

# 过滤数据

**关键字：**WHERE

WHERE子句放在FROM子句的后面，ORDER BY放在WHERE的后面

例子：SELECT prod_name,prod_price from products where prod_price=3.49;

WHERE子句的操作符：

- 等于=，不等于!=，不等于<>
- 小于<，大于>,小于等于<=，大于等于>=，不大于!>，不小于!<
- IS NULL 为空值，BETWEEN A AND B指定在A，B两个值之间（包含A，B）
- NULL 表示一种不确定，用=或者!=都会将它过滤掉

# 高级数据过滤

## 组合WHERE子句

**AND:**检索满足所有条件的行，

例如：select prod_id,prod_price,prod_name from products where vend_id="DLL01" and prod_price<=4;

**OR：**检索满足任一条件的行（有短路的效果）

*在SQL中，AND的优先级比OR高，可以加圆括号来改变条件结合的顺序*

例如：select prod_name,prod_price from products where (vend_id="DLL01" or vend_id="BRS01") and prod_price>=10;

## IN操作符

IN 操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN 取一组由逗号分隔、括在圆括号中的合法值。

例子：select prod_name,prod_price from products where vend_id in ("DLL01","BRS01") order by prod_name;

IN操作符实现的功能和OR相同，但是语法更加直观，而且性能更好

## NOT操作符

用来否定其后所跟的条件

# 使用通配符进行过滤

## LIKE操作符

**通配符：**用来匹配值的一部分的特殊字符

**搜索模式：**由字面值与通配符两者结合构成的搜索条件

通配符必须和LIKE操作符搭配使用，LIKE告知DBMS，后面所跟的搜索模式利用通配符而不是简单的相等匹配。通配符搜索只能用于文本字段（字符串）

## 通配符%

**通配符%：**任何字符出现任意次数。（包括0次）

例子：select prod_id,prod_name from products where prod_name like "Fish%"。表示找出prod_name以Fish开头的产品。

**注意**

* microsoft access中通配符是*

* 通配符在搜索模式中可以在任意位置使用，而且可以使用多次。

- 许多DBMS采用空格来填补内容，如果某列有50个字符，存储的文本为20个字符，系统会自动在存储的文本后面添加30个空格字符，使用通配符匹配以F开头以H结束的字符串，需要写成：F%H%*

* LIKE "%"不会匹配为NULL的行

## 通配符-

只能匹配单个字符。

例如：select prod_id,prod_name from products where prod_name like '__ inch teddy bear';

此处两个“_”，就匹配两个字符。8 inch teddy bear就不会被匹配到。

## 通配符[]

[]可以用来指定一个字符集，它可以匹配指定位置的一个字符。

例子：select cust_contact from customers where customers_contact like '[AB]%'

匹配以A或者B开头的。

此通配符可以和前缀字符^(脱字号)一起使用来否定。NOT当然也可以实现一样的功能。

例如：select cust_contact from customers where customers_contact like '[\^AB]%'

**注意：**

- 不是所有的DBMS都支持该通配符,只有微软的Access和SQL Server支持,mysql中不支持

- 与其他的操作符相比，通配符搜索起来更慢，应该避免使用
- 使用的时候也尽量不要将通配符放在开始处，这样最慢

# 创建计算字段

**计算字段：**计算字段不实际存在于数据库表中，计算字段运行在SELECT语句内创建的。

## 拼接字段

Access和SQL Server中使用"+"号，DB2,Oracle,PostgreSQL,SQLite,Open Office Base使用“||”

在MySQL中使用concat。

例子：select concat(vend_name," (",vend_country,")") from vendors order by vend_name;

去掉空格：

- 去掉字符串两边的空格：trim()

- 去掉左边的：ltrim()

- 去掉右边的：rtrim()

**别名（AS）：**可以为计算字段取一个别名，方便客户端引用。或者叫导出列

例如：select concat(vend_name," (",vend_country,")") as vend_title from vendors order by vend_name;

## 执行算数计算

例如：select prod_id,quantity,item_price,quantity*item_price as expanded_price 

​			from orderitems where order_num=20008;

此处的expanded_price就是一个计算字段，客户端可以引用。



sql还支持简单的运算，比如select 4*2。select trim(' abc ') 。select now()

# 使用函数处理数据

不同的DBMS函数有差异

- 提取字符串的组成部分：Access使用MID()；DB2,Oracle，PostgreSQL以及SQLite使用SUBSTR()；MySQL和SQL Server使用SUBSTRING()

- 数据类型转换：Access和Oracle每个类型转换有一个函数；DB2,PostgreSQL使用CAST()；MariaDB，MySQL，SQL Server使用CONVERT()

- 获取当前时间：Access使用NOW()；DB2,PostgreSQL使用CURRENT_DATE()；MariaDB，MySQL使用CURDATE()；ORACLE使用SYSDATE；SQL Server使用GETDATE()；SQLite使用DATE()

  

## 文本处理函数
- LEFT()：返回字符串左边的字符
- LENGTH()：返回字符串的长度
- LOWER()：转换为小写
- LTRIM()：去掉左边的空格
- RIGHT()：返回字符串右边的函数
- RTRIM()：去掉右边的空格
- SOUNDEX()：将任何文本串转换为描述其语音表示的字母数字模式的算法
- UPPER()：转换为大写

例如：select cust_name,cust_contact from customers 

​			where soundex(cust_contact)=soundex('Micheal Green');

返回：'Kids Place', 'Michelle Green'     因为这两个名字发音相似

## 日期和时间处理函数

不同的DBMS差异很大

例如：select order_num,order_date from orders where year(order_date)=2012;

可以用来提取2012年的订单

## 数值处理函数

ABS()，COS()，EXP()，PI()，SIN()，SQRT()，TAN()

# 汇总函数

## 聚集函数

- AVG()：返回某列的平均值
  - 例子：select avg(prod_price) as avg_price from products where vend_id="DLL01";
    - 返回的是vend_id为DLL01的产品的价格平均值
  - AVG()会忽视列值为NULL的行
- COUNT()：返回某列的行数
  - COUNT(*)：对表中行的数目进行计数，不会忽视NULL值
  - COUNT(column)：对特定列中行数进行统计，会忽视NULL值
- MAX()：返回某列的最大值
  - Max函数要求指定列名。可以用来找出指定列中最大的数值或者日期值；对于文本，可以返回列排序后最后一行
  - Max函数会忽视列值为NULL的行
- MIN()：返回某列的最小值
- SUM()：返回某列值之和
  - 会忽视NULL

以上的函数都可以指定DISTINCT，除了COUNT(*)

- 例如：select avg(distinct prod_price) as avg_price from products where vend_id="DLL01";

- Min,max也可以，但是没有意义

## 组合聚集函数

select count(*) as num_items,
			min(prod_price) as price_min,
			max(prod_price) as price_max,
			avg(prod_price) as price_avg
from products;



# 分组数据

分组的作用：将数据划分为多个逻辑组，然后就可以对每个组进行聚集计算

## 创建分组

**GROUP BY**

例子： select vend_id,count(*) as num_prods from products group by vend_id;

对分组后的每个组计算一次，而不是对整个表

**注意：**  

- 分组可以嵌套，从而进行更加细致的分组
- 如果在 GROUP BY 子句中嵌套了分组，数据将在最后指定的分组上进行汇总
- 如果在select中使用表达式，则必须在group by子句中使用相同的表达式，不可以使用别名
- 大多数SQL实现不允许group by列带有长度可变的数据类型
- 除了聚集计算语句外，select语句中每一列都必须在group by子句中给出
- 如果分组中包含具有NULL值的行，则所有NULL值的行将作为一个分组返回
- Group by子句必须出现在where子句之后，order by 子句之前

## 过滤分组

**having:**作用和where很类似，但是where过滤的是行，having过滤的是分组（另一种理解：where在分组之前进行行过滤，having在数据分组之后进行分组过滤），如果不指定group by，DBMS会同等的对待两者。

例子：select cust_id,count(*) as orders from orders group by cust_id having count(*)>=2;

**同时使用where和having：**

select vend_id,count(\*) as num_prods 

from products 

where prod_price>=4 

group by vend_id 

having count(\*)>=2;

**注意：在使用group by之后，也应该给出order by子句,来保证数据正确排序的唯一方法**

例子：

select order_num,count(\*) as items 

from orderitems 

group by order_num 

having count(\*)>=3 

order by items,order_num;

**select子句的顺序：**

select--->from--->where--->group by--->having--->order by



# 子查询

例子：需要列出订购物品“RGAN01”的所有顾客

select cust_name,cust_contact 
from customers 

where cust_id in (select cust_id 

​								from orders 

​								where order_num in (select order_num 

​																		from orderitems 

​																		where prod_id="RGAN01"));

**注意：**

- 子查询是自内向外处理的

- 作为子查询的select语句只能查询单个列

## 作为计算字段使用子查询

例子：需要显示customers表中每个顾客的订单总数

select cust_name,
	   cust_state,
	   (select count(*) 
        from orders 
		where orders.cust_id=customers.cust_id) as orders
from customers
order by cust_name;

**采用句点"."来分隔表名和列名，可以避免出现歧义**

 # 联结表

为什么将数据分解成多个表存储：更高效，更方便处理，伸缩性更好。

## 创建联结

例子：

select vend_name,prod_name,prod_price 

from vendors,products 

where vendors.vend_id=products.vend_id;

完全限定列名，避免了来自多个表中相同列名之间的歧义。

Where子句起到了过滤的作用，如果没有where子句的限制，返回的结果是两个表的笛卡尔积（叉联积）。

### 内联结

例子：

select vend_name,prod_name,prod_price 

from vendors inner join products 

on vendors.vend_id=products.vend_id;

实现的效果和上一个相同，但是两个表之间的关系是用inner join指定的，过滤条件使用on给出

### 联结多个表

例子：

select prod_name,vend_name,prod_price,quantity 
from orderitems,products,vendors
where products.vend_id=vendors.vend_id
and orderitems.prod_id=products.prod_id
and order_num=20007;

**表的联结操作非常耗费资源，联结的表越多，性能下降越多**

例子：查询订购产品RGAN01的顾客列表

- 采用子查询

  - select cust_name,cust_contact 
    from customers 

    where cust_id in (select cust_id 

    ​								from orders 

    ​								where order_num in (select order_num 

    ​																		from orderitems 

    ​																		where prod_id="RGAN01"));

- 采用联结
  - select cust_name,cust_contact
    from customers,orders,orderitems
    where orderitems.prod_id="RGAN01"
    and orders.order_num=orderitems.order_num
    and customers.cust_id=orders.cust_id;

# 创建高级联结

## 使用表别名

使用别名理由：

- 缩短SQL语句
- 允许在一条SELECT语句中多次使用相同的表

注意：表的别名只在查询中使用，而列的别名可以返回客户端

## 自联结、自然联结、外联结

### 自联结

例子：

- 使用子查询：
  - select cust_id,cust_name,cust_contact
    from customers
    where cust_name=(
    select cust_name
    from customers
    where cust_contact="Jim Jones"
    );

- 使用联结
  - select c1.cust_id,c1.cust_name,c1.cust_contact
    from customers as c1,customers as c2
    where c1.cust_name=c2.cust_name and c2.cust_contact="Jim Jones";

### 自然联结

使用内联结会返回所有数据，相同的列甚至会多次出现，自然联结可以克服这一点。

怎么做：系统不会自动完成，需要程序猿自己来做。

自然联结要求你只能选择哪些唯一的列，一般对一个表使用通配符，而对其他表的列使用明确的子集来完成。

例子：

select C.*,O.order_num,O.order_date,OI.prod_id,OI.quantity,OI.item_price
from customers as C,orders as O,orderitems as OI
where C.cust_id=o.cust_id
and OI.order_num=O.order_num
and prod_id="RGAN01";

### 外联结

联结在相关表中没有关联行的行。（OUTER JOIN）

==例子：（左外联结）==

select customers.cust_id,orders.order_num
from customers left outer join orders
on customers.cust_id=orders.cust_id;

结果为：

cust_id, order_num
'1000000001', '20005'
'1000000001', '20009'
**'1000000002', NULL**
'1000000003', '20006'
'1000000004', '20007'
'1000000005', '20008'

==例子：（内联结）==

select customers.cust_id,orders.order_num
from customers inner join orders
on customers.cust_id=orders.cust_id;

结果为：

cust_id, order_num
'1000000001', '20005'
'1000000001', '20009'
'1000000003', '20006'
'1000000004', '20007'
'1000000005', '20008'

**左外联结**：从左边的表中选择所有的行

**右外联结**：从右边的表中选择所有的行

**全外联结**：包行两个表中不关联的行

注意：Access、MariaDB、MySQL、open office base和SQLite不支持full outer join

## 使用带聚集函数的联结

例子：

select customers.cust_id,count(orders.order_num) as num_ord
from customers inner join orders
on customers.cust_id=orders.cust_id
group by customers.cust_id;
结果：
cust_id, num_ord
'1000000001', '2'
'1000000003', '1'
'1000000004', '1'
'1000000005', '1'



select customers.cust_id,count(orders.order_num) as num_ord
from customers left outer join orders
on customers.cust_id=orders.cust_id
group by customers.cust_id;

结果：

cust_id, num_ord
'1000000005', '1'
'1000000004', '1'
'1000000003', '1'
**'1000000002', '0'**
'1000000001', '2'



# 组合查询

SQL允许执行多个查询（多条SELECT语句），并将结果作为一个查询结果集返回，这些组合查询成为“并（union）”或复合查询。

使用组合查询的情况：

- 从不同的表中返回数据结果作为一个查询结果（可能要与别名一起使用）
  - 对一个表执行多个查询，按一个查询返回数据（和多个where子句效果相同）


## 创建组合查询

UNION操作符可以将多条SELECT语句的结果合并为一个结果集（UNION放在各条SELECT语句之间）

例子：

select cust_name,cust_contact,cust_email
from customers
where cust_state in ('IL','IN','MI')
**union**
select cust_name,cust_contact,cust_email
from customers
where cust_name='Fun4All';

**等价于：**

select cust_name,cust_contact,cust_email
from customers
where cust_state in ('IL','IN','MI')
or cust_name='Fun4All';

==注意：==

- n条SELECT语句需要n-1个union连接(n>=2)
- Union中的每个select语句的查询必须包含相同的列、表达式、聚集函数（各列的出现顺序可以不一样）
- 列数据类型必须兼容（包含可以隐式转换的类型）
- UNION对于不同查询结果中重复的行只会返回一次（这一点和WHERE一样），使用UNION ALL可以返回重复行
- 组合查询中ORDER BY只能出现一次，放在语句的最后，对组合查询的最终结果进行一次排序。（如果需要在某个select中使用ORDER BY，那么需要使用括号将对应的SELECT语句括起来）



# 插入数据

**INSERT：**用来将行插入（或添加）到数据库表中。

插入方式：

- 插入完整的行
- 插入行的一部分
- 插入某些查询结果

### 插入完整的行

==方法一：==

insert into customers
values(
'1000006','Toy Land','123 any street','new york','ny','111','USA',NULL,NULL);

这种方法各列必须和表定义中出现的次序保持一致，不推荐

==方法二：==

**insert into** customers(cust_id,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,
cust_email)
**values**('10001006','Toy Land','123 any street','new york','ny','111','USA',NULL,NULL);

明确的给出列名，列的值和指定的列名的次序保持一致。推荐的做法

方法二可以给出每个列，也可以给出部分列，没有给出的列在定义时允许为NULL值。

如果该列不允许有NULL值，但是省略了，此时DBMS会产生消息错误。

### 插入检索出的数据

由INSERT和SELECT语句组成，可以插入一行数据，也可以插入多行数据。

例子：

**Insert into** customers(

cust_id,cust_contact,cust_email)

**select** cust_id,cust_contact,cust_email

from custnew;

实际上Insert和select中给出的列名可以不一样，DBMS按照次序进行填充

Insert, select语句中的select也可以包含where进行筛选操作。

## 从一个表复制到另外一个表

**SELECT INTO**

DB2不支持

例子：（MariaDB,Oracle,PostgreSQL,SQLite）

create table custcopy as
select * from customers;

创建了一个新表custcopy，然后将customers表的整个内容复制到了新表中，用通配符*，表示复制所有的列。

注意select语句可以使用任何子句，比如where和group by，也可以联结操作从多个表中插入数据。（都插入到表custcopy中）



# 更新和删除数据

## 更新数据

**UPDATE**:更新（修改）表中的数据，可以更新表中的特定行/所有行

例子：

update customers
set cust_email="kim@qq.com",
	   cust_contact="NULL"
where cust_id="10000005";

要删除某个列的值，将其设置为NULL即可。如果不添加where，该语句会更新所有行

## 删除数据

**DELETE：**可以删除所有的行，也可以删除特定行，但是DELETE语句不可以删除表本身

**TRUNCATE TABLE 表名**：可以更快的删除表中所有行

例子：

delete from customers
where cust_id='10000006';

## 更新和删除的原则

- 使用带有WHERE的UPDATE和DELETE
- 确保每个表都有主键
- 在使用UPDATE和DELETE之前，可以先用SELECT语句测试，确保要更新删除的是正确的记录



# 创建和操纵表

例子：

create table products123(
		prod_id char(10) ==not null==,
		vend_id char(10) ==not null,==
		prod_name char(254) ，
		prod_price decimal(8,2) not null default 0 ,
		prod_desc varchar(100)
);

这里的not null说明了该列不可以为null，default 0指定了该列的默认值是0

**注意：**在创建新表的时候，指定的表名必须不存在，否则会出错（不会自动覆盖旧表，需要手动删除旧表，然后再创建）

### NULL值

每个表列要么是NULL列，要么是NOT NULL列，在创建表的时候指定

允许NULL值的列允许在插入行的时候不给出该列的值，不允许NULL值的列不接受没有列值的行。

**只有不允许NULL值的列可以作为主键（主键是表示表中每一行的列）**

NULL值和空字符串不一样，NULL值是没有值。



