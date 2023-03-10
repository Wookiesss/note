# MySQL

## SQL概述和特点

> SQL全称：Structured Query Language，是结构化查询语言,用于**访问和处理数据库的标准的计算机语言**
>

- 具有综合统一性，不同数据库支持的SQL稍有不同
- 非过程化语言
- 语言简洁，用户容易接受
- 以一种语法结构提供两种使用方式



**语法特点：**

- SQL对关键字的大小写不敏感
- SQL语句可以以单行或者多行书写,以分号结束
- SQL的注释：

```mysql
-- 单行注释(--后面一定要加一个空格)
# 单行注释(#后面可加可不加空格)
/*
多行注释
多行注释
*/
```



## 数据类型

### 数值类型

![1](images\MySQL\1.png)

无符号的写法：在数据类型后面加 `UNSIGNED`或`unsigned`

### 字符串类型

![2](images\MySQL\2.png)

### 日期和时间类型

![3](images\MySQL\3.png)



## DDL

> Data Definition Language，数据定义语言，用来定义数据库对象
>

**数据库操作**

- `show databases;` 查询所有数据库的名称
- `create database [if not exists] 数据库名;` 创建数据库
- `use 数据库名;` 切换使用数据库
- `drop database [if exists] 数据库名;` 删除数据库
- `alter database 数据库名 character set 字符集名;` 修改数据库编码 

**表操作**

- 创建表

```mysql
create table [if not exists] 表名(
字段名1 类型[(宽度)] [约束条件] [comment'字段说明'],
字段名2 类型[(宽度)] [约束条件] [comment'字段说明'],
字段名3 类型[(宽度)] [约束条件] [comment'字段说明']
)[表的一些设置];
```

查询

- `show tables;` 查询当前数据库的所有表名称
- `show create table 表名;` 查询指定某个表的创建语句
- `desc 表名;` 查询表结构

删除

- `drop table 表名;` 删除表 
- `alter table 表名 drop 字段名;`删除列

添加

- `alter table 表名 add 字段名 数据类型(长度) [约束];`  添加列

修改

- `alter table 表名 change 旧字段名 新字段名 数据类型(长度) [约束];` 修改列名和类型
- `alter table 表名 character set 字符集名称` 修改表的字符集
- `rename table 表名 to 新表名;` 修改表名



## DML

> Data Manipulation Language，数据操作语言，用来对数据库中表的数据记录进行更新（增删改）
>

**增加数据**

```mysql
-- 向表中添加某些列和值 （列名和值要一一对应）
insert into 表(列名1,列名2,列名3...) values(值1,值2,值3...);
-- 向表中添加所有列的值
insert into 表名 values(值1,值2,值3...);
```

**数据修改**

```mysql
-- 如果不加任何条件，则会将表中所有数据全部修改
-- where 条件 表示满足该条件的数据才会被修改
update 表名 set 列名1 = 值1,列名2 = 值2,...[where 条件]

-- 范例：
-- 将所有学生的地址修改为重庆
update student set address = '重庆';
-- 将id为1004的学生的地址修改为北京
update student set address = '北京' where id = 1004;
```

**数据删除**

```mysql
-- 删除表中满足条件的数据（如不加条件则删除全部数据）
delete from 表名 [where 条件];
-- 清空表中全部数据（本质上是先删除表，再创建新表）
truncate [table] 表名;
```





## DQL

> Data Query Language，数据查询语言，用来查询数据库中表的数据

**基本查询**

```mysql
-- 查询所有数据
select * from 表名;
-- 查询指定字段数据
select 字段1,字段2... from 表名;
-- 别名查询 关键字as(可省略)
select 字段名 别名 from 表名 as 别名;
-- 去除重复值distinct
select distinct * from 表名;
-- 运算查询(只进行运算输出，不改变原表中 的值)
select 字段名(运算符) from 表名;
```



**条件查询**

<img src="images\MySQL\4.png" alt="4" style="zoom: 80%;" />

<img src="images\MySQL\5.png" alt="5" style="zoom:90%;" />

![6](images\MySQL\6.png)

```mysql
-- 示例：
-- 查询商品价格在200到1000之间所有商品
select * from product where price >= 200 && price <=1000;
select * from product where price between 200 and 1000;
-- 查询第二个字为'蔻'的所有商品
select * from product where pname like '_蔻%';
```



**排序查询**

`select 字段名1,字段名2... from 表名 order by 字段名1 [asc|desc],字段名2[asc|desc]...;`

- asc代表升序，desc代表降序，如果不写默认升序
- 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序
- order by子句，放在查询语句的最后面



**聚合查询**

| 聚合函数 |       功能说明       |
| :------: | :------------------: |
| count()  | 统计指定字段记录行数 |
|  sum()   | 计算指定字段的数值和 |
|  max()   | 计算指定字段的最大值 |
|  min()   | 计算指定字段的最小值 |
|  avg()   | 计算指定字段的平均值 |

- 聚合函数的运算，忽视NULL值的存在



**分组查询**

`select 字段1,字段2... from 表名 group by 分组字段 having 分组条件;`

- where 和 having 的区别：
- where在分组之前进行限定，不满足条件则不参与分组
- having在分组之后进行限定，不满足则被过滤
- where不能对聚合函数进行判断，而having可以



**分页查询**

 `select 字段1,字段2... from 表名 limit n;`   显示前n条 

 `select 字段1,字段2... from 表名 limit m,n ` 分页显示

- m：整数表示从第几条索引开始，起始索引从0开始。  计算方式 （查询页码-1）* 每页显示条数
- n：整数，表示查询多少条数据



**将一张表的数据导入到另一张表中**

- insert into select语句（表2必须存在）


`insert into 表2(字段1,字段2...) select 字段1,字段2... from 表1;`

`insert into 表2 select * from 表1;`

- select into from语句（表2不存在）

`select 字段1,字段2 into 表2 from 表1;`



**正则表达式**

`regex '正则表达式'`

正则表达式的符号表示与Java中相同



## DCL

> Data Control Language，数据控制语言，用来管理数据库用户、控制数据库的访问权限

**用户管理**

查询用户：`use mysql;`         `select * from user;`

创建用户：`create user '用户名'@'主机名' identified by '密码';`

修改用户密码：`alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';`

删除用户：`drop user '用户名'@'主机名'`

> 主机名可用 % 通配

**权限控制**

查询权限：`show grants for‘用户名'@'主机名;`

授予权限：`grant 权限列表 on 数据库名.表名 to ‘用户名'@'主机名';`

撤销权限：`revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';`



## SQL编写和执行顺序

SQL编写顺序：select -> from -> where -> group by -> order by -> limit

SQL执行顺序：from -> where -> group by -> select -> having -> order by -> limit



## 函数

### 字符串函数

![7](images\MySQL\7.png)

### 数值函数

![8](images\MySQL\8.png)

### 日期函数

![9](images\MySQL\9.png)

### 流程函数

![10](images\MySQL\10.png)





## 约束

> constraint，实际上就是表中数据的限制条件
>

**作用：**保证表中的记录完整性和有效性

### 主键约束(primary key)

- 关键字：primary key
- 主键约束是一个列或者多个列的组合，其值能唯一地标识表中的每一行，可以提高查询的效率
- 相当于 唯一约束+非空约束 的组合，主键约束列不允许重复，也不允许出现空值
- 每个表最多只允许一个主键
- 当创建主键的约束时，系统默认会在所在的列和列组合上建立对应的唯一索引。

**添加单列主键**

```mysql
-- 方式一：在定义字段的同时指定主键
create table 表名(
    字段名 数据类型 primary key
);
-- 方式二：在定义字段后再指定主键
create table 表名(
    ...
    [constraint [约束名]] primary key [字段名]
);
```

**添加多列主键（联合主键）**

```mysql
-- 当主键是由多个字段组成时，不能直接在字段名后面声明主键约束，只能在最后指定
-- 添加数据时：只要多个字段内容不完全相同且都不为NULL即可
create table 表名(
    ...
    [constraint [约束名]] primary key(字段1,字段2,...,字段n)
);
```

**创建完表后添加主键**

```mysql
-- 可添加单列主键，也可添加联合主键
alter table 表名 add primary key(字段列表);
```

**删除主键**

```mysql
alter table 表名 drop primary key;
```



### 自增长约束(auto_increment)

> 通过给字段添加 AUTO_INCREMENT 属性来实现主键自增长
>

格式：`字段名 数据类型 AUTO_INCREMENT`

- 默认情况下，AUTO_INCREMENT 的初始值是 1，每新增一条记录，字段值自动加 1。
- 一个表中只能有一个字段使用 AUTO_INCREMENT 约束，且该字段必须有唯一索引，以避免序号重复
- AUTO_INCREMENT 约束的字段必须具备 NOT NULL 属性
- AUTO_INCREMENT 约束的字段只能是整数类型
- AUTO_INCREMENT 约束字段的最大值受该字段的数据类型约束，如果达到上限就会失效

**指定自增字段初始值**

```mysql
-- 方式一：创建表时指定
create table 表名(
    字段名 数据类型 primary key auto_increment
)auto_increment = 初始值;
-- 方式二：创建表之后指定
create table 表名(
    字段名 数据类型 primary key auto_increment
);
alter table 表名 auto_increment = 初始值;
```

**细节：**delete和truncate在删除后自增列的变化

- delete数据之后自动增长从断点开始
- truncate数据之后自动增长都从默认起始值1开始



### 非空约束(not null)

> 使字段的值不能为空
>

- 创建表时指定：`字段名 数据类型 not null`
- 创建表后指定：`alter table 表名 modify 字段名 数据类型 not null;`
- 删除非空约束：`alter table 表名 modify 字段名 数据类型;`



### 唯一约束(unique)

> 使所有数据中字段的值不能重复出现
>

- 创建表时指定：`字段名 数据类型 unique`
- 创建表后指定：`alter table 表名 add constraint 约束名 unique(字段名);`
- 删除唯一约束：`alter table 表名 drop index 约束名/字段名;`



### 默认约束(default)

> 用来指定字段的默认值
>

- 创建表时指定：`字段名 数据类型 default 默认值`
- 创建表后指定：`alter table 表名 modify 字段名 数据类型 default 默认值;`
- 删除默认约束：`alter table 表名 change column 字段名 数据类型 default null;`

​                                  `alter table 表名 modify 字段名 数据类型 default null;`



### 零填充约束(zerofill)

添加数据时，当该字段的值的长度小于定义的长度时，会在该值的前面补上相应的0

zerofill默认为int(10)，且默认会自动加 unsigned 属性

- 指定：`字段名 int zerofill`
- 删除：`alter table 表名 modify 字段名 int;`



### 外键约束(foreign key)

- 创建表时：`[constraint 外键名] foreign key 外键字段名 references 主表名 主键字段名; `
- 创建表后：`alter table 从表名 add constraint 外键名 foreign key(外键字段名>) references 主表名(主键字段名); `
- 删除约束：`alter table 从表名 drop foreign key 外键约束名; `







## 多表操作

### 多表关系

**一对一：**一张合并表

**一对多：**在多的一方建立外键，指向一的一方的主键

**多对多：**借助第三张中间表，中间表至少包含两个字段，将多对多的关系，拆成一对多的关系，中间表至少要有两个外键，这两个外键分别指向原来的那两张表的主键



### 多表查询

**交叉连接查询**

- 交叉连接查询返回被连接的两个表所有数据行的笛卡尔积
- 笛卡尔积可以理解为一张表的每一行去和另外一张表的任意一行进行匹配
- 假如A表有m行数据，B表有n行数据，则返回m*n行数据
- 笛卡尔积会产生很多冗余的数据，后期的其他查询可以在该集合的基础上进行条件筛选

格式：`select * from 表1,表2,表3...;`  



**内连接查询**

> 查询的是多张表的交集部分

隐式内连接：`select 字段列表 from 表1,表2... where 条件;`

显式内连接：`select 字段列表 from 表1 [inner] join 表2 on 条件;`



**外连接查询**

- 左外连接：

  - `select 字段列表 from 表1 left [outer] join 表2 on 条件;`

  - 查询左表(表1)的所有数据，包含表1和表2的交集部分；右表(表2)无对应数据补NULL

- 右外连接：

  - `select 字段列表 from 表1 right [outer] join 表2 on 条件;`

  - 查询右表(表2)的所有数据，包含表1和表2的交集部分；左表(表1)无对应数据补NULL

- 满外连接：

  - `select 字段列表 from 表1 full [outer] join 表2 on 条件;`

  - 左右表匹配的数据 + 左表没有匹配到的数据 + 右表没有匹配到的数据

  - 由于MySQL不支持SQL99的满外连接语法，Oracle是支持的，所以用union关键字表示并集

    `左外连接语句 union [all] 右外连接语句`   不加all表示去重



**子查询**

> 包含select嵌套的查询

- 标量子查询

  子查询返回的结果是单个值，可作为条件，用运算符去判断查询  `=  , > , >= , < , <=`

- 列子查询

  子查询返回的结果是一列（可以是多行)，常用操作符  `IN 、NOT IN、ANY 、 SOME 、 ALL` 

- 行子查询

  子查询返回的结果是一行（可以是多列)，常用的操作符   `= 、>、IN 、NOT IN`

  `select * from 表名 where(字段名1,字段名2...) = (select嵌套语句);` 

- 表子查询

  子查询返回的结果是多行多列





## 事务

> 一组操作的集合，不可分割的最小操作单位。多个操作同时进行，要么同时成功，要么同时失败

### 事务操作

查看/设置事务提交方式：`select @@autocommit;`      `set @@autocommit = 0;`

>  1代表自动提交，0代表手动提交

开启事务：`select transaction;` 或 `begin;`

提交事务：`commit;`

回滚事务：`rollback;`

### 事务四大特性

- 原子性(Atomicity)：事务是不可分割的最小操作单位，要么同时成功，要么同时失败
- 一致性(Consistency)：事务完成时，必须使所有的数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，对数据库中的数据的改变是永久的

### 并发事务问题

- 脏读：一个事务读取到另一个事务中还没有提交的数据
- 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同
- 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在

### 事务隔离级别

|       隔离级别        | 脏读 | 不可重复读 | 幻读 |
| :-------------------: | :--: | :--------: | :--: |
|   Read uncommitted    |  √   |     √      |  √   |
|    Read committed     |  x   |     √      |  √   |
| Repeatable Read(默认) |  x   |     x      |  √   |
|     Serializable      |  x   |     x      |  x   |

查询事务隔离级别 : `select @@transaction_isolation;`

设置事务隔离级别 : `set [session / global] transaction isolation level 隔离级别名;`

- session：应用于当前session (当前 cmd 窗口) 内的所有事务
- global：应用于所有session (全局) 中的所有事务，且当前已经存在的session不受影响
- 事务隔离级别越高，数据越安全，但性能越低



## 存储引擎
