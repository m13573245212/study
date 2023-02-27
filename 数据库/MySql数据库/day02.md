#day02
##使用eclipse时,sql乱码问题
在建立连接时,修改url,在数据库名称的后面添加字符集补充,实例如下:mysql：loaclhost:3306/db2?useUnicode=true&characterEncoding=UTF-8
##主键约束
主键约束的特点:唯一并且非空,一个表中只能有一个主键  
- 如何使用:

	create table t1(id int primary key,name varchar(10));
- 以下报错,主键约束导致id不可相同

        insert into t1 values (1,'小明');
    	insert into t1 values (1,'小明');
- 以下报错,主键约束导致id不可为空

		insert into t1(name) values ('小花');
    	
##主键+自增
- 如何使用:
	
		create table t2(id int primary key auto_increment,age int);
1. 当自增字段的值为null时会自动赋值并自增
2. 以表中出现的最大值加1
3. **删除数据自增数值不减,内部维护一个计数机制,计数器只增不减,当主键设置null,其自增时取历史值的最大值,生成主键值.**
##注释 comment
- 在创建表的时候可以通过comment对字段进行描述

		create table t3(id int primary key auto_increment comment '这里是主键id',comm  int comment'这是奖金');
- 如何查看注释

	show create table t3;
##`和'的区别
- `是在创建表时,修饰表名和字段名的,可以省略

		create table `t4`(id int,`age` int);
- '是用来表示字符串的
##数据冗余
- 如果数据库设计不合理,保存大量数据后会出现大量的重复数据,这种现象称为数据冗余,通过拆分表格的形式,把可能大量重复的数据,用单独一张表保存,在原表中只需要通过id建立关系即可.

###练习:
####创建商品表,商品id,商品名称,商品价格,商品分类,库存数量
create table item(id int primary key auto_increment,name varchar(10),price int,categoryid int,num int);
####创建分类表category,分类id、分类的名称、该分类的上级分类
create table category(id int primary key auto_increment,name varchar(10),parentid int);
####表中插入 电器分类下的电视分类 康佳电视 价格3580 库存25
	insert into category values(null,'电器',null);
	insert into category values (null,'电视机',1);
	select * from category;
	
	insert into item values(null,'康佳电视',3580,2,25);
	select * from item;
###练习:
####设计表保存以下数据:保存教学部下java教研部的老师苍老师,工资200,年龄18,然后保存集团总部下销售部,销售A部的员工李然老师,工资50,年龄28
需要部门表 部门id 一级部门分类的id 二级部门分类(从属于一级)
需要老师员工表 id 姓名 工资 年龄 部门id

##事务
- 什么是事务?事务是数据库中执行sql语句的最小工作单元,在同一事务中的sql语句要么同时成功,要么同时失败.
- mysql数据默认sql语句是自动提交的
- 如何开启事务?  

		create table person(id int,name varchar(10),money int);
		insert into person values(1,'超人',500),(2,'钢铁侠',1000);
		- 超人找钢铁侠借300
		1. 超人+300
		   update person set money=800 where id=1;
		2. 钢铁侠-300
		   update person set money=700 where id=2;
- 关闭数据库的自动提交
	- 查看自动提交的状态:  
		show variables like '%autocommit%';
	- 关闭自动提交  
		set autocommit=0;
	- 开启自动提交  
		set autocommit=1;
    - 验证转账流程:  
    	update person set money=800 where id=1;  
		update person set money=700 where id=2;
	- 打开新终端,查看是否修改(应该没改,因为此时尚未提交)
	- 回到原窗口执行commit;之后,再去新终端查看(此时应该提交了修改)
- 回滚 rollback  
	执行rollback会回滚到上次提交的点或者关闭自动提交时的点.(准确的说是数据库里做修改后 （update,insert,delete）未commit 之前,使用rollback,可以恢复数据到修改之前)
- 保存回滚点 savepoint s1;  
- 指定回滚点 rollback to s1;
#SQL分类
##DDL Data Definition Language数据定义语言
- 包括:create drop alter truncate(删除表的指令)
- 不支持事务
##DML Data Manipulation Language 数据操作语言
- 包括:insert update delete select()
- 支持事务
##DQL Data Query Language数据查询语言
- 只有select,也属于DML
##TCL Transaction Control Language 事务控制语言
- 包括:commit, rollback, savepoint, rollback to
##DCL Data Control Language 数据控制语言
- 分配用户权限的相关sql
###truncate
- 格式:truncate table 表名;
- 作用:删除表并创建一张空表,auto_increment数值清零
##数据库数据类型
###整型
- 常用:int(m) bigint(m) m代表显示长度,如果字段数值长度不到m,会在数值前面补零,但是一定要和zerofill结合使用.
	例如:create table t_int(num int(10) zerofill);
###浮点数
- 常用:double(m,d) m代表总长度,d代表小数长度
- decimal(m,d)超高精度小数
###字符串
- char(m):长度不可变  abc 占20 执行效率高 最大值255
- varchar(m):长度可变 abc 占3  节省资源  最大值65535,但超过255建议使用text
- text:可变长度最大65535
###日期类型
- date:只能保存年月日
- time:时分秒
- datatime:年月日时分秒   9999-12-31 默认为空
- timestamp:年月日时分秒  最大2038-01-19 默认值当前时间












