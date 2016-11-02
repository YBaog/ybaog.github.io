---
layout: post
title: Mysql之触发器(trigger) 
categories: Mysql
description: 触发器(trigger)：监视某种情况，并触发某种操作。
keywords: Mysql, trigger ,触发器
---
MySQL包含对触发器的支持。触发器是一种与表操作有关的数据库对象，当触发器所在表上出现指定事件时，将调用该对象，即表的操作事件触发表上的触发器的执行。

### 什么是触发器

**官方定义**

触发器（trigger）是个特殊的存储过程，它的执行不是由程序调用，也不是手工启动，而是由事件来触发，比如当对一个表进行操作（ insert，delete， update）时就会激活它执行。触发器经常用于加强数据的完整性约束和业务规则等。 触发器可以从 DBA_TRIGGERS ，USER_TRIGGERS 数据字典中查到。

触发器有一个非常好的特性就是:触发器可以禁止或回滚违反引用完整性的更改，从而取消所尝试的数据修改。

什么意思，举个例子解释一下，街机游戏大家都玩过吧，闯过一关，闯下一关，有一关没闯过就要从第一关开始。触发器根这个类似。

**官方解释如下**

触发程序视为单一交易中的一部份，因此可以由原触发程序还原交易，如果在交易过程中侦测到严重的错误（如使用者中断连线），则会自动还原整个交易。

他的作用很明显了，可以保重数据的完整性，下面有一个实例来说明他的好处，以及如果使编写代码不那么复杂

**触发器创建语法四要素**

> 1.监视地点(table) 
> 2.监视事件(insert/update/delete) 
> 3.触发时间(after/before) 
> 4.触发事件(insert/update/delete)

### 语法

```mysql

create trigger triggerName

after/before insert/update/delete on 表名

for each row   #这句话在mysql是固定的

begin

sql语句;

end;

```

首先我们来创建两张表：

#商品表

```
create table g

(

　　id int primary key auto_increment,

　　name varchar(20),

　　num int

);

```

#订单表

```
create table o

(

　　oid int primary key auto_increment,

 　 gid int,

     much int

);

```

`insert into g(name,num) values('商品1',10),('商品2',10),('商品3',10);`

  如果我们在没使用触发器之前：假设我们现在卖了3个商品1，我们需要做两件事

  1.往订单表插入一条记录

`insert into o(gid,much) values(1,3);`

  2.更新商品表商品1的剩余数量

`update g set num=num-3 where id=1;`

现在，我们来创建一个触发器：

需要先执行该语句：delimiter $(意思是告诉mysql语句的结尾换成以$结束)

```mysql

create trigger tg1
after insert on o
for each row 
begin
update g set num=num-3 where id=1;
end$

```
这时候我们只要执行：

`insert into o(gid,much) values(1,3)$`

会发现商品1的数量变为7了，说明在我们插入一条订单的时候，触发器自动帮我们做了更新操作。

但现在会有一个问题，因为我们触发器里面num和id都是写死的，所以不管我们买哪个商品，最终更新的都是商品1的数量。

比如：我们往订单表再插入一条记录: `insert into o(gid,much) values(2,3)`,执行完后会发现商品1的数量变4了，而商品2的数量没变，这样显然不是我们想要的结果。我们需要改改我们之前创建的触发器。

我们如何在触发器引用行的值，也就是说我们要得到我们新插入的订单记录中的gid或much的值。

对于insert而言，新插入的行用`new`来表示，行中的每一列的值用`new.列名来表示`。

所以现在我们可以这样来改我们的触发器

```mysql

create trigger tg2
after insert on o
for each row 
begin
update g set num=num-new.much where id=new.gid;(注意此处和第一个触发器的不同)
end$

```
第二个触发器创建完毕，我们先把第一个触发器删掉

`drop trigger tg1$`

再来测试一下，插入一条订单记录: `insert into o(gid,much) values(2,3)$`

执行完发现商品2的数量变为7了，现在就对了。

现在还存在两种情况：

1.当用户撤销一个订单的时候，我们这边直接删除一个订单，我们是不是需要把对应的商品数量再加回去呢？

2.当用户修改一个订单的数量时，我们触发器修改怎么写?

我们先分析一下第一种情况：

监视地点：o表

监视事件：delete

触发时间：after

触发事件：update

对于delete而言：原本有一行,后来被删除，想引用被删除的这一行，用old来表示，old.列名可以引用被删除的行的值。

那我们的触发器就该这样写：

```mysql

create trigger tg3

after delete on o

for each row

begin

update g set num = num + old.much where id = old.gid;(注意这边的变化)

end$

```

创建完毕。

再执行`delete from o where oid = 2$`

会发现商品2的数量又变为10了。

第二种情况：

监视地点：o表

监视事件：update

触发时间：after

触发事件：update

对于update而言：被修改的行，修改前的数据，用old来表示，`old.列名`引用被修改之前行中的值；

修改的后的数据，用new来表示，new.列名引用被修改之后行中的值。

那我们的触发器就该这样写：

```mysql

create trigger tg4

after update on o

for each row

begin

update g set num = num+old.much-new.much where id = old/new.gid;

end$

```

先把旧的数量恢复再减去新的数量就是修改后的数量了。

我们来测试下：先把商品表和订单表的数据都清掉，易于测试。

假设我们往商品表插入三个商品，数量都是10，

买3个商品1: `insert into o(gid,much) values(1,3)$ `

这时候商品1的数量变为7；

我们再修改插入的订单记录: `update o set much = 5 where oid = 1$`

我们变为买5个商品1，这时候再查询商品表就会发现商品1的数量只剩5了，说明我们的触发器发挥作用了。

我们先做个测试：

假设：假设商品表有商品1，数量是10；

我们往订单表插入一条记录：

`insert into o(gid,much) values(1,20);`

会发现商品1的数量变为-10了。这就是问题的所在，因为我们之前创建的触发器是after,也就是说触发的语句是在插入订单记录之后才执行的，这样我们就无法判断新插入订单的购买数量。

### after和before的区别

after 是先完成数据的增删改，再触发，触发的语句晚于监视的增删改操作，无法影响前面的增删改动作；也就是说先插入订单记录，再更新商品的数量；

before 是先完成触发，再增删改，触发的语句先于监视的增删改，我们就有机会判断，修改即将发生的操作；

我们用一个典型案例来区分它们的区别，新建一个触发器：

#监视地点: 商品表o

#监视事件：insert

#触发时间：before

#触发事件：update

案例: 当新增一条订单记录时，判断订单的商品数量，如果数量大于10，就默认改为10

```mysql

create trigger tg6

before insert on o

for each row

begin

　　if new.much > 10 then

　　　　set new.much = 10;

　　end if;

　　update g set num = num - new.much where id = new.gid;

end$

```

执行完，把之前创建的after触发器删掉，再来插入一条订单记录：

`insert into o(gid,much) valus(1,20)$`

执行完会发现订单记录的数量变为10，商品1的数量变为0了，就不会出现负数了。

### 触发器的优点

- 1，触发器的"自动性"

对程序员来说，触发器是看不到的，但是他的确做事情了，如果不用触发器的话，你更新了user表的name字段时，你还要写代码去更新其他表里面的冗余字段，

我举例子，只是一张表，如果是几张表都有冗余字段呢，你的代码是不是要写很多呢，看上去是不是很不爽呢。

- 2，触发器的数据完整性

触发器有回滚性，举个例子，我发现我很喜欢举子，就是你要更新五张表的数据，不会出现更新了二个张表，而另外三张表没有更新。

但是如果是用php代码去写的话，就有可能出现这种情况的，比如你更新了二张表的数据，这个时候，数据库挂掉了。你就郁闷了，有的更新了，有的没更新。

这样页面显示不一致了，变有bug了。

**每天进步一点点。**