# MySQL数据库

### 关系型数据库

1.支持复杂的SQL语句的查询

2.支持事物

#### 非关系型数据库

1.NOSQL 不需要经过SQL层的处理 性能高

2.可扩展性 因为是键值对的形式 所以水平扩展非常的容易



## 一、进入数据库

-u user用户名

-p password 密码

-h host 主机名

```
启动mysql服务
net start 你的mysql实例的名字

进入数据库：
mysql -hlocalhost -uroot -p

mysql -uroot -p
->密码

```

root超级管理员 可以创建和管理其他的用户，root用户不可以远程登录或访问



## 二、对数据库的操作

CREATE 创建

DROP 删除

ALTER 修改

SHOW 展示

####(1)查看所有数据库

show databases

#### (2）创建数据库

create databases 库名

create database if not exists库名；防止创建同名的库出现错误

### (3)查看创库语句

show create databases库名

#### （4）使用数据库（进入到当前的库中）

use库名

#### （5）查看当前所在的数据库

select database()；

#### （6）数据库的删除

drop database库名

drop database if not exists库名；删除数据库如果该库如果存在 防止报错

#### （7）创建数据库并设置字符集

create database 库名character set utf8；

#### （8）创建数据库 并设置字符集

alter database hzpython1803 character set 字符集；

## 三、对于表的操作

1.查看所有的表

show tables；

2.创建表

```
mysql> create table if not exists fs(
    -> id int unsigned primary key auto_increment,
    -> username varchar(20),
    -> sex tinyint,
    -> age tinyint unsigned,
    -> info varchar(100)
    -> );
```

3.查看表结构

desc表名

4.删除表中的某个字段

alter table fs drop字段名

5.删除表

drop table if exists 表名；删除表如果表存在

6.查询表

select * from mytime

## 四、MySQL表的创建

#### 字段类型

#### （1）整形

| 类型       | 大小     | 范围           | 无符号范围       | 用途        |
| -------- | ------ | ------------ | ----------- | --------- |
| tinyint  | 1字节    | -128，127     | 0，255       | 小整数值      |
| smallint | 2字节    | -32768,32767 | 0,65535     | 大整数值      |
| int      | 4字节    | 2->10位       | 4.......10位 | 大整数值      |
| float    | 4字节    |              |             | 单精度浮点型    |
| double   | 8字节    |              |             | 双精度浮点型    |
| decimal  | 根据存储的值 |              |             | 小数值（更加精准） |

浮点数中的m代表当前存储的长度，n代表小数的位数 m-n代表整数的位数  超出则报错

##### 浮点数后面数字的意义：

1.整形后面给定数字，并不是限定当前存储值得长度，并没有任何意义，除非配合可选参数zerofill零填充才有意义

2.字符串类型后面给定的长度，则是限制当前存储数据的长度

3.整形默认长度会比本身长度大1，因为是符号位；

#### （2）字符串类型

| 类型            | 大小       | 用途                  |
| :------------ | -------- | ------------------- |
| char          | 0-255    | 存储定长字符串             |
| varchar       | 0-255    | 存储不定长度的字符串          |
| text          | 0-65535  | 长文本数据               |
| blob          | 0-65535  | 存储二进制长文本数据          |
| enum('w','m') | 65535个成员 | 枚举：只能选取括号中的某一个值进行存储 |
| set（‘w','m'）  | 64个成员    | 集合：可以选择多个成员         |

##### char和varchar区别：

1.char和varchar的存储长度都是0-255

2.char的执行效率高于varchar

3.varchar要比char更节省存储空间

4.当char存储的值达不到指定的长度时，则使用空来占位

##### enum和set区别

1.enum只能选择其中的一个值

2.set可以选择多个值 多个值使用逗号来隔开

#### (3)时间和日期

| 类型       | 大小   | 范围                                      | 格式                  | 用途       |
| -------- | ---- | --------------------------------------- | ------------------- | -------- |
| date     | 3    | 1000-01-01 - 9999-12-31                 | YYY-MM-DD           | 日期       |
| time     | 3    | -838:59:59 - 838:59:59                  | HH:MM:SS            | 时间值或持续时间 |
| year     | 1    | 1901/2155                               | YYYY                | 年份值      |
| datatime | 8    | 1000-01-01 00:00:00/9999-12-31 23:59:59 | YYY-MMM-DD HH：MM：SS | 存储时间和日期  |



## 五、字段约束

1.unsigned无符号 正数 

 	只能用于数值类型不允许出现负数 存储长度会扩大一倍

2.zerofill 零填充

​	只能用于数值类型 当指定的位数不足的时候  零填充

3.default 默认值

​	如果当前字段没有传值 则值为默认值（不设定默认值 默认值为null）

4.null和not null

​	默认为null 当不插入值则插入为null，当设置当前字段为 not null的时候，则该字段必须传值

5.comment 设置当前字段的说明

6.auto_increment 自增



 alter table myint add uage tinyint unsigned after age在字段后面增加一条

修改默认值：







注意：

1.SQL语句以分号作为结束

2.SQL命令 不区分大小写

3.数据库的切换使用use

4.\c撤销上条命令

5.\G竖着查看

6.当遇到在终端中不管怎样输入命令都不执行 name查看一下左侧，是否为->

7.在window下不区分库，表名的大小写 但是Ubuntu下区分

8.退出数据库的几种方式

 \q  exit  quit



## 六、null注意事项

1.null意味着没有值 或者 未知值

2.可以测试某个值是否为null  is null

3.不能对null进行算术运算

4.所有和null进行运算的都为null

## 七、mysql的索引

1.主键索引   primary key

2.唯一索引  unique

3.常规索引  index

4.全文索引   fulltext

#### （1）主键索引

主键索引是数据库中最常见的索引类型 主要确定数据表里数据记录的位置  给字段添加primary key来确定索引值

注意：

1.每个表中，只能有一个主键索引

2.每个表中最好有一个主键索引 并不是必须的

3.主键索引可以有很多的候选项 （auto_increment,not null)

4.当表中的数据 被删除以后 auto_increment依然记录着下一个数据插入的行号值

​	truncate表名 清空表 并将自增归位

​	alter表名 auto_increment=1

##### 实例

```python
mysql> create table myindex(
	-> id int unsigned primary key auto_increment not null
    -> );

```



#### （2）唯一索引

唯一索引和主键索引都一样 不能插入重复的值  不同的是主键索引一个表只能存在一个, 唯一索引可以存在多个

##### 实例

```python
mysql> create table myunique(
	-> username varchar(20) unique not null
    -> );

```



#### （3）常规索引

常规索引  只为提高数据的查询效率来使用的 

缺点：

1.提高了查询效率  但是降低了增删改查的效率..文件   占用磁盘空间





#### （4）全文索引

fulltext

alter table 表名 add fulltext  索引名称（索引字段）

##### 创建表并添加索引的完整写法

```python
mysql> create table user(
    -> id int unsigned primary key auto_increment not null,
    -> username varchar(20) unique,
    -> age tinyint,
    -> index(age)
    -> #unique(username)
    -> );
    #给索引添加索引名称
    mysql> create table user2(
        -> username varchar(20),
    	-> age tinyint,
    	-> unique myname(username),
    	-> index aindex(age)
    	-> );
```



## 八、表的存储类型

MyISAM和InnoDB

###### 修改类型

alter table 表名 engine=表存储类型；

#### MyISAM和innoDB的区别

1.myisam表的存储文件为3个，innoDB为2个

2.myisam不支持事物 innodb支持

3.myisam不支持外键 innoDB支持

4.myisam表的查询效率高于innodb 但是innodb的安全性高于myisam



### （1）MyISAM的文件说明

1.frm文件  存储当前表结构的文件

​	在innodb和myisam中都存在，

1. .MYD：即MY DATA 存储表数据的文件
2. .MYI：即MY INDEX 存储表索引的文件

### （2）innoDB的文件说明

1. .frm文件  存储当前表结构的文件

   在innodb和myisam中都存在

2. .ibd  存储表数据和索引

### （3）InnoDB事物处理操作

##### 1.将当前表文件存储类型改为innodb

​	alter table表名 engine=innodb；

##### 2.查看当前表的提交类型

​	select  @@autocommit

​	如果值为1则为自动提交

##### 3.改为手动提交（开启事物）

​	set autocommit=0

##### 4.事物开始

​	begin

##### 5.执行各种SQL语句

##### 6.提交或者回滚

​	1.commit work；

​	2.rollback work；

***注意***

事物是针对库中表的数据来操作的 并不是针对你当前的库 如果库被干掉，那就一切都不存在



## 九、更改表结构

##### 1.给当前的表添加一个新的字段 add

​	alter table user add sex enum('w','m') default 'w';

##### 2.添加新字段在某个字段的后面 after

​	alter table user add info varchar(100) default '个人说明'after id；

##### 3.添加新字段在第一位 first

 	alter table user add infoop varchar(100) default'个人说明‘ first；

##### 4.更改字段名称 并更改字段顺序 change

​	alter table user change infoop money decimal(6,2);

​	alter table user change money money decimal(6,2) after username;



##### 5.更改字段类型 modify

​	alter table user modify username char(11);



##### 6.添加字段索引

alter table user add index(username); #添加常规索引

alter table user add unique（username）；

alter table user add index infoindex(info)；添加索引并添加索引名称



##### 7.删除索引

alter table user drop key username；



##### 8.创建一个表b和表a

create table b like a；

##### 9.查看所有的索引

​	show index from 表名

##### 10.更改字段的字符集

​	alter table user modify username varchar(20) character set utf8;



## 十、数据的添加

##### 1.指定字段名称添加数据

​	insert into user(username,age,info) values('张三'，18，’个人说明‘);

​	需要注意：如果有字段不为空且没有默认值  那么必须插入值

##### 2.不指定字段添加值（需要将所有字段都插入）

​	insert into user values(null,"lisi",20,"个人说明");

##### 3.指定字段添加多个值

insert into user(username,ago,info) values("df",21,"fefd"),("jdfisf",25,"fhuwefh");

##### 4.不指定字段添加多个值

insert into user values(,,,,,,,,),(,,,,,,,,,,,,),,,,,,,;



## 十一、SELECT 数据的查询

##### 1.不指定字段查询(查询所有字段)

select * from 表名

##### 2.指定字段查询

select 字段名1，字段名2......from表名  ；

##### 3.查询字段并运算

select id+age from user;

##### 4.起别名

select username as name from 表名；

select username name from表名；



## 十二、where 条件

##### （1）比较运算符

1.>

2.<

3.>=

4.<=

5.!=/<>

6.=

##### (2)逻辑运算符

##### 1.逻辑与 and

select * from user where username='苍老师' and age<=30;

##### 2.逻辑或 or

select * from user where username='苍老师' or age>=30;

##### 3.and 和 or一起使用

select * from user where id=1 or (age=18 and sex='w');

##### 4.在....内 in

select * from user where id in(1,3,5,6,7); #和下面的写法一个意思

select * from user where id=1 or id=2 or id=6; 

##### 5.不在.....内 not in

select * from user where id not in(1,3,5,6,7); #和下面的写法一个意思

select * from user where id!=1 and id!=2 and id!=6; 

##### 6.在....范围内 between....and....

select * from user where age between 20 and 33;和下面的写法一个意思

select * from user where age>=20 and age<=33;

##### 7.不在.....范围内  not between.....and.......

select * from user where age not between 20 and 33;和下面的写法一个意思

 select * from user where age not between 20 and 33;

## （3）order by 排序

##### 1.默认升序  asc

select * from user order by age;

select * from user order by age asc;

##### 2.降序

select * from user order by age desc;

## （4）is   is not

1.is not

select * from user where username is not null;

2.is

select * from user where username is null;

## （5）limit

limit num 直接取出num条数据

select * from order by age desc limit 1； #取出年龄最大的一条数据

limit x,num  从x的位置取出num条数据

##### 注意：

如果是从头取出num条数据 limit num == limit 0 ,num；

##### 分页计算：

求分页取值的偏移量

(nowPage-1)*everyPage

## （6）模糊查询 like

1.包含查询

like ‘%字符%’

2.以某个字符开头的查询

like‘字符%’

3.以某个字符作为结尾的查询

like‘%字符‘



## 十三、聚合函数

1.最大值

max()

select max(age) from user;

2.最小值

min()

select min(age) from user;

3.统计

count()

select count(*) from user;



4.平均值

avg()

select avg(age) from user;

5.求和

sum()

select sum(age) from user;



## 十四、group by分组

##### 查询男女分别多少人

select sex,count(*) from user group by sex;

##### 统计每个年龄段分别有多少人

select age,count(*) from user group by age;

##### 统计每个年龄段的男女分别有多少

select sex,age,count(*) from user group by sex,age;

### group by having  其中having相当于where

##### 查询每个年龄段人数大于1人的数据

select age,count(*) as count from user group by age having count>1;

##### 查询每个年龄段人数大于1人的数据，并且年龄小于40

select age,count(*) as count from user group by age having count>1and age<40;

##### 查询每个年龄段人数大于1人的数据 并且年龄为10 20 30

select age,count(*) as count from user group by age having age in (10,20,30)



## 十五、delete删除

##### 主体结构

delete from 表名[where.......]

##### 注意：

where 应该加 如果没有where条件删除所有数据

##### 实例

delete from user where username=’xxx‘；



## 十六、update修改

##### 主题结构

update 表名 set 字段名 = 字段值，[字段名=字段值....[where.........]]

##### 注意：

不要忘记where条件   否则修改全部

##### 实例

update user set sex =’m' where age in (18,20);

## 十七、多表联查

##### 关联条件

外键

#### （1）隐式内连接

select * from user,goods where user.id = goods.uid

select user.username,user.age,goods.goodsname from user,goods where user.id=goods.uid

##### 起别名

select u.username,g.goodname from user u,goods g where u.id = g.uid

#### (2)显式内连接 inner join on

select * from user INNER JOIN goods ON user.id=goods.uid and goods.uid=1

select u.username,g.goodsname from user u INNER JOIN goods g ON u.id = g.uid and g.uid = 1;

##### 注意：

隐式连接和显示连接其实是同一个查询  会将关联的数据全部查询出来



#### （3）左链接  left join on

select u.username,g.goodsname from user u LEFT JOIN goods f ON u.id = g.uid

##### 注意：

左链接会将左表作为主表，右表作为辅助   会将主表的所有数据查询出来 辅表没有关联的数据使用null来占位

select * from goods g left JOIN user u on u.id=g.uid



#### (4) 右链接  right join  on

select u.username,g.goodsname from user u right JOIN goods g ON u.id=g.uid

select * from  user u right JOIN goods g on u.id=g.uid



## 十八、以下代码作为了解

##### (1) 使用mysql库

use mysql;

##### (2) 查询当前有哪些用户

select user from user;

##### (3) 创建其它用户

create user zhangsan identified by '123456';

##### (4) 赋予权限

grant all on hz03.* to zhangsan;

all 代表所有权限

##### (5) 回收权限

revoke all on hz03.* from zhangsan;

##### (6) 删除用户

drop user zhangsan;









